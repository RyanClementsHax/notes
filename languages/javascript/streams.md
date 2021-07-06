# Streams

## Errors
- I've had issues handling errors in streams because they're callback based
- in my opinion, its best to try to handle errors within the scope of promises
- when piping, it is also handy to pipe errors through as well because they could then be handled by the destination pipe rather than having to worry about multiple pipes throwing errors
- the problem of errors can be very tricky because multiple pipes can throw the same error causing one to be caught by the promise and another to be considered uncaught (yes this can really happen, it gave me a headache for several days)
- in conclusion, test your piping code THOROUGHLY

## Passthrough
- these streams are handy for connecting streams together
- very good when some stream implementations need time to "initialize" before sending data causing errors with code that expects them to work like other streams
  - all you have to do is pipe the stream that needs to "initialize" into a passthrough stream, and hand that passthrough stream around as if it were the other stream

## Multiple readers
- multiple readers on one stream is useful but has problems
  - on error, a stream "unpipes" meaning it won't get any data during that unpipe time even if you repipe immediately
  - one slow reader will hold up all of the other readers
- consider using a transform stream for what you want to do if you're able