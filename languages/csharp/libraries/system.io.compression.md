# System.IO.Compression

## ZipArchive

- has [problems](https://github.com/dotnet/runtime/issues/17912) with creating zip entries that have proper file permissions
- there is no way to lazily create the zip file
  - `ZipArchive` has to "push" the contents directly into some stream or buffer and has no concept of backpressure
  - unless you want to pool the contents in memory, you need to hook it up directly to the destination to write to