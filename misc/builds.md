# Builds

## Concurrent builds
- when running java (and perhaps other languages too) builds that output to the same directory, be sure that only one is running at a time even when it is building the same project
- this created odd build time errors like `TruncatedClassFileException` or being unable to find the class file that happened intermittently
- if you could get the build to complete, the code could run, but you will get very odd runtime and logic errors that are impossible to reproduce locally
