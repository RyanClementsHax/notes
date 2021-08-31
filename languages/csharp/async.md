# Async

## Best practices

- [AsyncGuidance.md](https://github.com/davidfowl/AspNetCoreDiagnosticScenarios/blob/master/AsyncGuidance.md) by David Fowl
- Asynchrony is viral
  - if you use `async`/`await`, use it in the entire call stack
- Async void
  - if you want to fire and forget, always wrap the function that returns the task in a `Task.Run(SomeBackgroundOperationAsync)`
  - not doing so will lead to problems like if an exception is thrown, it will crash your app instead of being handled by the `TaskScheduler.UnobservedTaskException`
- Prefer `Task.FromResult` over `Task.Run` for pre-computed or trivially computed data
  - this avoid unnecessary queueing in the thread pool for a result that will be computed almost immediately
  - use `ValueTask<T>` to avoid allocating a `Task` on the managed heap
- Avoid using `Task.Run` for long running work that blocks the thread
  - you don't want to steal a thread away from the pool to handle this
  - manually create a new thread instead of stealing a thread pool thread
- Avoid using `Task.Result` and `Task.Wait`
  - "sync over async"
  - this causes 2 threads to be used instead of 1 and can lead to thread pool starvation
  - can lead to deadlocks
- Prefer `await` over `ContinueWith`
  - it doesnt capture the `SynchronizationContext` and thus is semantically different than `async`/`await`
  - plus you can more easily avoid the pyramid of doom
- Always create `TaskCompletionSource<T>` with `TaskCreationOptions.RunContinuationsAsynchronously`
  - not doing so will lead to the typical starvation/deadlock problems
- Always dispose `CancellationTokenSource`(s) used for timeouts
  - this puts pressure on the timer queue if not disposed
- Always flow `CancellationToken`(s) to APIs that take a `CancellationToken`
  - in order for the whole point of cancelling a task to work well, the token source needs to be used by the whole call stack
- Cancelling uncancellable operations
  - see the docs
- Using a timeout
  - need to cancel timers if operation sucessfully succeeds, otherwise you could end up with lots of timers which can flood the timer queue
- Always call `FlushAsync` on `StreamWriter`(s) or `Stream`(s) before calling `Dispose`
  - this is to handle async disposing
  - use `await using (var thing = new Thing())`
- Prefer `async`/`await` over directly returning `Task`
  - asynchronous and synchronous exceptions are normalized to always be asynchronous
  - the code is easier to modify
  - diagnostics of asynchronous methods are easier
  - exceptions thrown will be automatically wrapped in the returned task instead of surprising the call with an actual exception

## `Task.WaitAll`

- give it an `IEnumerable<Task>` and it will block until all of the tasks complete

## `Task.WhenAll`

- the async version of `Task.WaitAll`

## `Task.Run`

- avoid using this without cancellation tokens because otherwise you will have runaway threads
- use this to call cpu bound tasks
- avoid using in cpu bound method to create "fake" asyncronicity
  - let the caller decide how to mitigate this
  - doing so communicates that the method is "inherently" asynchronous and will lead to confusion when the thread pool is starved

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

## [Processing tasks as they complete](https://devblogs.microsoft.com/pfxteam/processing-tasks-as-they-complete/)

- [doing this performantly](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/concepts/async/start-multiple-async-tasks-and-process-them-as-they-complete)
- doing this performantly and nicely

  ```cs
  public static class TaskExt
  {
      public static IAsyncEnumerable<T> Race<T>(this IEnumerable<Task<T>> tasks)
      {
          var channel = Channel.CreateUnbounded<T>();

          Task.Run(async () =>
          {
              foreach (var task in tasks)
              {
                  #pragma warning disable CS4014 // Because this call is not awaited, execution of the current method continues before the call is completed
                  task.ContinueWith(async x => await channel.Writer.WriteAsync(await x), CancellationToken.None, TaskContinuationOptions.ExecuteSynchronously, TaskScheduler.Default);
                  #pragma warning restore CS4014 // Because this call is not awaited, execution of the current method continues before the call is completed
                  await Task.Delay(120);
              }
          });

          return channel.Reader.ReadAllAsync();
      }
  }
  ```
