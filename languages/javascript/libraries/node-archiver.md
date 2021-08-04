# Node Archiver

## Errors

- if an error happens in the archive (e.g. `archive.on('error', () => {})`) it won't end the archiving process (e.g. `archive.finalize()` resolves without problem)
  - to fix this, you can use this helper function

    ```ts
    // pass in an archive you are creating
    function finalizeArchiveSafely(archive: Archiver): Promise<void> {
        return new Promise((resolve, reject) => {
            // if we dont reject on error, the archive.finalize() promise will resolve normally
            // and the error will go unchecked causing the application to crash
            archive.on('error', reject)
            archive.finalize().then(resolve).catch(reject)
        })
    }
    ```
