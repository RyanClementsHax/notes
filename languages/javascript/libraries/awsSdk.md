# AWS SDK

## S3

### [s3.getObject(params).createReadStream() timeouts](https://github.com/aws/aws-sdk-js/issues/2087)
- TLDR: if no traffic goes over the connection, it timesout
- Fix: don't open your streams until you need them (i.e. lazy load them)

### Zip file creation
- using this sdk, we can stream files from s3, into [archiver](https://github.com/archiverjs/node-archiver), and back into s3 without causing any data to hit disk or accumulate in memory
  - it should be noted that zipping directly to the user's browser would probably be a better solution because it doesn't run into any problems with what to do with the zip file in s3 (e.g. how long do you keep it for? how do you deal with the double download transfer costs?)
- [this](https://github.com/miratronix/s3-readable-stream) seems to be one attempt at simplifying streams to s3, but idk if it works
- [this](https://gist.github.com/amiantos/16bacc9ed742c91151fcf1a41012445e) is a working example implemented for in lambda
    ```js
    // Lambda S3 Zipper
    // http://amiantos.net/zip-multiple-files-on-aws-s3/
    //
    // Accepts a bundle of data in the format...
    // {
    //     "bucket": "your-bucket",
    //     "destination_key": "zips/test.zip",
    //     "files": [
    //         {
    //             "uri": "...", (options: S3 file key or URL)
    //             "filename": "...", (filename of file inside zip)
    //             "type": "..." (options: [file, url])
    //         }
    //     ]
    // }
    // Saves zip file at "destination_key" location

    "use strict";

    const AWS = require("aws-sdk");
    const awsOptions = {
    region: "us-east-1",
        httpOptions: {
            timeout: 300000 // Matching Lambda function timeout
        }
    };
    const s3 = new AWS.S3(awsOptions);
    const archiver = require("archiver");
    const stream = require("stream");
    const request = require("request");

    const streamTo = (bucket, key, resolve) => {
        var passthrough = new stream.PassThrough();
        s3.upload(
            {
                Bucket: bucket,
                Key: key,
                Body: passthrough,
                ContentType: "application/zip",
                ServerSideEncryption: "AES256"
            },
            (err, data) => {
            if (err) throw err;
                console.log("Zip uploaded");
                resolve();
            }
        ).on("httpUploadProgress", (progress) => {
            console.log(progress);
        });
        return passthrough;
    };

    // Kudos to this person on GitHub for this getStream solution
    // https://github.com/aws/aws-sdk-js/issues/2087#issuecomment-474722151
    const getStream = (bucket, key) => {
        let streamCreated = false;
        const passThroughStream = new stream.PassThrough();

        passThroughStream.on("newListener", event => {
            if (!streamCreated && event == "data") {
                const s3Stream = s3
                    .getObject({ Bucket: bucket, Key: key })
                    .createReadStream();
                s3Stream
                    .on("error", err => passThroughStream.emit("error", err))
                    .pipe(passThroughStream);

                streamCreated = true;
            }
        });

        return passThroughStream;
    };

    exports.handler = async (event, context, callback) => {
        var bucket = event["bucket"];
        var destinationKey = event["destination_key"];
        var files = event["files"];

        await new Promise(async (resolve, reject) => {
            var zipStream = streamTo(bucket, destinationKey, resolve);
            zipStream.on("error", reject);

            var archive = archiver("zip");
            archive.on("error", err => {
                throw new Error(err);
            });
            archive.pipe(zipStream);

            for (const file of files) {
                if (file["type"] == "file") {
                    archive.append(getStream(bucket, file["uri"]), {
                        name: file["filename"]
                    });
                } else if (file["type"] == "url") {
                    archive.append(request(file["uri"]), { name: file["filename"] });
                }
            }
            archive.finalize();
        }).catch(err => {
            throw new Error(err);
        });

        callback(null, {
            statusCode: 200,
            body: { final_destination: destinationKey }
        });
    };
    ```
    - this implementation doesn't quite handle errors well though
      - you will get uncaught promise rejections or uncaught error issues if something fails like if an s3 object its trying to zip doesn't exist
    - when maxed out on hardware, it does ~1Gig/min
- [here](https://gist.github.com/amiantos/16bacc9ed742c91151fcf1a41012445e#gistcomment-3804034) is a couple of snippets from a more robust implementation (much omitted do to it being proprietary)
    ```ts
    // handler.ts
    import { S3Client } from '@aws-sdk/client-s3'
    import { S3Service } from './s3Service'
    import archiver from 'archiver'
    import type { Archiver } from 'archiver'

    const s3 = new S3Client({})
    const s3Service = new S3Service(s3)

    interface File {
        fileName: string
        key: string
    }

    interface ZipEvent {
        bucket: string
        destinationKey: string
        files: File[]
    }

    const finalizeArchiveSafely = (archive: Archiver): Promise<void> => {
        return new Promise((resolve, reject) => {
            // if we dont reject on error, the archive.finalize() promise will resolve normally
            // and the error will go unchecked causing the application to crash
            archive.on('error', reject)
            archive.finalize().then(resolve).catch(reject)
        })
    }

    export default async (event: ZipEvent) => {
        const archive = archiver('zip')

        try {
            for (const file of event.files) {
                const downloadStream = s3Service.createLazyDownloadStreamFrom(event.bucket, file.key)
                archive.append(
                    downloadStream,
                    {
                        name: file.fileName
                    }
                )
            }

            // for whatever reason, if we try to await the promises individually, we get problems with trying to handle the errors produced by them
            // for example, if we tried to await them individually and injected some error like told it to zip an object that didn't exist, both statements would throw
            // one would be caught by the try catch and the other would be considered an unhandled promise rejection (bizarre, I know, but I kid you not)
            // using Promise.all was the only solution that seemed to solve the issue for us
            await Promise.all([
                finalizeArchiveSafely(archive),
                s3Service.uploadTo(event.bucket, event.destinationKey, archive)
            ])
        // with the robustness added, all errors will be caught by this catch block
        // so no need to worry about unhandled promises or unhandled exceptions
        } catch (e) {
            // this makes sure that the archive stops archiving if there is an error
            archive.abort()
            throw e
        }
    }
    ```
    ```ts
    // s3Service.ts
    import { PassThrough } from 'stream'
    import { GetObjectCommand } from '@aws-sdk/client-s3'
    import { Upload } from '@aws-sdk/lib-storage'

    import type { S3Client } from '@aws-sdk/client-s3'
    import type { Readable } from 'stream'

    export class S3Service {
        constructor(private s3: S3Client) {}

        // we need to lazy load the streams because archiver only works on one at a time
        // if we create a stream to an object in s3, the connection will time out when no traffic is going over it
        // this will be the case if multiple streams are opened at the same time and one of them is for a very large object
        public createLazyDownloadStreamFrom(bucket: string, key: string): Readable {
            let streamCreated = false
            // create a dummy stream to pass on
            const stream = new PassThrough()
            // only when someone first connects to this stream do we fetch the object and feed the data through the dummy stream
            stream.on('newListener', async event => {
                if (!streamCreated && event == 'data') {
                    await this.initDownloadStream(bucket, key, stream)
                    streamCreated = true
                }
            })

            return stream
        }

        public async uploadTo(
            bucket: string,
            key: string,
            stream: Readable
        ): Promise<void> {
            const upload = new Upload({
                client: this.s3,
                params: {
                    Bucket: bucket,
                    Key: key,
                    // we pipe to a passthrough to handle the case that the stream isn't initialized yet
                    Body: stream.pipe(new PassThrough()),
                    ContentType: 'application/zip'
                }
            })
            await upload.done()
        }


        // if we don't pipe the errors into the stream, we will get unhandled exception errors in the console and won't be caught by any try/catch or .catch constructs that call createLazyDownloadStreamFrom since this initDownloadStream function is called in the callback function of .on('newListener') and thus isn't in the "call stack" of the call to createLazyDownloadStreamFrom
        // for example, it is totally reasonable that the s3 object asked for doesn't exist
        // in which case s3.send(new GetObjectCommand(/* */)) throws
        private async initDownloadStream(
            bucket: string,
            key: string,
            stream: PassThrough
        ) {
            try {
                const { Body: body } = await this.s3.send(
                    new GetObjectCommand({ Bucket: bucket, Key: key })
                )
                // we need to type narrow here since Body can be one of many things
                if (!body) {
                    stream.emit(
                        'error',
                        new Error(
                            `got an undefined body from s3 when getting object ${bucket}/${key}`
                        )
                    )
                } else if (!('on' in body)) {
                    stream.emit(
                        'error',
                        new Error(
                            `got a ReadableStream<any> (a stream used by browser fetch) or Blob from s3 when getting object ${bucket}/${key} instead of Readable`
                        )
                    )
                } else {
                    body.on('error', err => stream.emit('error', err)).pipe(stream)
                }
            } catch (e) {
                stream.emit('error', e)
            }
        }
    }
    ```