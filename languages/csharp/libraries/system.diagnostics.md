# System.Diagnostics

## Stopwatch
- handy class for timing
- you can start it with `.Start()` and stop it with `.Stop()`
- by default, it won't reset the timer when you call `.Stop()`
- to reset it and not have it accumulate time, you need to call `.Reset()`