# Async

## `Task.WaitAll`
- give it an `IEnumerable<Task>` and it will block until all of the tasks complete

## `Task.WhenAll`
- the async version of `Task.WaitAll`

## `Channel`
- acts as an async concurrent message queue
- creating one
    ```cs
    // unbounded
    var unboundedChannel = Channel.CreateUnbounded<string>();
    // bounded to 5 items
    var boundedChannel = Channel.CreateBounded<string>(capacity: 5);
    ```
- bounded channels will suspend the producers if the channel is at capacity until capacity opens
- reading
    ```cs
    var msg = await channel.Reader.ReadAsync().AsTask()
    ```
- writing
    ```cs
    await channel.Writer.WriteAsync(msg).AsTask();
    ```