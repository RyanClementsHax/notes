# Streams

## Passthrough
- these streams are handy for connecting streams together
- very good when some stream implementations need time to "initialize" before sending data causing errors with code that expects them to work like other streams
  - all you have to do is pipe the stream that needs to "initialize" into a passthrough stream, and hand that passthrough stream around as if it were the other stream