# System.IO.Compression

## ZipArchive

- has [problems](https://github.com/dotnet/runtime/issues/17912) with creating zip entries that have proper file permissions
- there is no way to lazily create the zip file
  - `ZipArchive` has to "push" the contents directly into some stream or buffer and has no concept of backpressure
  - unless you want to pool the contents in memory, you need to hook it up directly to the destination to write to

### Unzipping a stream

- be warned that this is a pushed based stream so if you are using a memory stream to gather the outputs, know that large streams will blow out the program's memory
- if you want to unzip to some place out of memory (e.g. disk, s3), have give `ZipArchive` the stream directly

```cs
using System.IO;
using System.IO.Compression;
// ^^^ requires a reference to System.IO.Compression.dll
static class Program
{
    const string path = ...
    static void Main()
    {
        using(var file = File.OpenRead(path))
        using(var zip = new ZipArchive(file, ZipArchiveMode.Read))
        {
            foreach(var entry in zip.Entries)
            {
                using(var stream = entry.Open())
                {
                    // do whatever we want with stream
                    // ...
                }
            }
        }
    }
}
```
