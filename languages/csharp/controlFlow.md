# Control Flow

## Switch statements

- pattern matching doesn't allow for blocks on pattern match like regular switch statements do
- if you don't put a `break;` statement in your `default` clause, you will see the compiler complain about [control cannot fall through from one case label (default) to another](https://stackoverflow.com/questions/28336401/control-cannot-fall-through-from-one-case-label-default-to-another-in-c-sha/28336415)
