# Javascript

## Turn based processing
- this is how many event driven systems like web browsers and UI frameworks are written
- Javascript is single threaded so all threads of execution need to share the same thread using "turns"
- law of turns: Never wait. Never block. Finish fast.
- because turn based processing unwinds the stack after every turn and there are no blocks, exceptions from IO can't be "thrown"
  - this is because the stack trace is list
  - this is a big reason why promises are used