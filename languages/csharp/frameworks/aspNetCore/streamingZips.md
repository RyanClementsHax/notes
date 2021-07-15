# Streaming Zips

- streaming a zip to the client without pooling the zip contents in memory is difficult at the time of writing this
- this is because there is no async zip api in c#
- the `ZipArchive` provided in `System.IO.Compression` has no concept of backpressure and provides no api to lazily create the zip stream

## Using custom IActionResult
- the current implementation of `FileStreamResult` assumes that the stream passed in can be read from incrementally (as with most streams)
  - specifically, it eventually comes to [this](https://github.com/dotnet/aspnetcore/blob/39dccd7ca27389c12dfd877996afde55ff10fc00/src/Http/Shared/StreamCopyOperationInternal.cs#L36) function which causes the problem for us
  - this is bad for streaming zips, because `ZipArchive` writes directly to a stream and provides no stream we can read from
- this solution uses internals that the aspnet core team thought would be useful to make public ("pubternals" - David Fowler) in case anyone wanted to use them, but these, being internals, could change any time
  - it uses the fundamentals that Stephen Cleary describes in his [blog post](https://blog.stephencleary.com/2016/11/streaming-zip-on-aspnet-core.html) on this topic
- the idea is that we create our own custom `IActionResult` that evaluates the stream the way we need it to in order to avoid pooling the zip in memory with a memory stream
  - `ZipArchive` needs to "push" the zip directly to the body of the response if you want to stream directly to the client
- Stephen Cleary's [implementation](https://github.com/StephenClearyExamples/AsyncDynamicZip/tree/core-ziparchive) works well and is good for anyone who doesn't care about duplicating logic into their own executor and doesn't care about not being able to use the [helper functions](https://github.com/dotnet/aspnetcore/blob/main/src/Mvc/Mvc.Core/src/ControllerBase.cs#L1305) that `ControllerBase` provides for returning `FileStreamResult`s
- the following implementation replaces the default `FileStreamResultExecutor` with one that extends it and overrides the one method we need to change to make it work for this edge case thus allowing for all of the other file stream handling logic (like header setting, and logging) to not have to be duplicated

### `FileCallbackResultExecutor.cs`
- this executor overrides the static method that we need to override
- unfortunately, this requires us to override the instance method that calls it since you can't just override static methods without hiding them
```cs
/// <summary>
/// An <see cref="IActionResultExecutor{FileStreamResult}"/> for a file stream result.
/// </summary>
/// <remarks>
/// <para>It does everything the <see cref="FileStreamResultExecutor"/> does but uses <see cref="Stream.CopyToAsync(Stream, System.Threading.CancellationToken)" /> to write to the response body if the stream is a <see cref="CallbackStream" />.</para>
/// <para>This is to avoid having to pool the contents of the <see cref="CallbackStream" /> in memory before piping it to the response body.</para>
/// <para>This workaround will no longer be necessary when the following issue is fixed: https://github.com/dotnet/runtime/issues/1541</para>
/// </remarks>
public class FileCallbackResultExecutor : FileStreamResultExecutor
{
    /// <summary>
    /// Initializes a new <see cref="FileCallbackResultExecutor"/>.
    /// </summary>
    /// <param name="loggerFactory">The factory used to create loggers.</param>
    public FileCallbackResultExecutor(ILoggerFactory loggerFactory)
        : base(loggerFactory)
    {
    }

    /// <summary>
    /// Write the contents of the FileStreamResult to the response body.
    /// </summary>
    /// <remarks>
    /// <para>
    /// This is an exact copy of <see cref="FileStreamResultExecutor.WriteFileAsync(ActionContext, FileStreamResult, RangeItemHeaderValue, long)" />
    /// but calls the internal implementation of <see cref="FileCallbackResultExecutor.WriteFileAsync(HttpContext, Stream, RangeItemHeaderValue?, long)" /> to pipe the stream to the response body.
    /// </para>
    /// </remarks>
    /// <param name="context">The <see cref="ActionContext"/>.</param>
    /// <param name="result">The FileStreamResult to write.</param>
    /// <param name="range">The <see cref="RangeItemHeaderValue"/>.</param>
    /// <param name="rangeLength">The range length.</param>
    protected override Task WriteFileAsync(
        ActionContext context,
        FileStreamResult result,
        RangeItemHeaderValue? range,
        long rangeLength)
    {
        if (context == null)
        {
            throw new ArgumentNullException(nameof(context));
        }

        if (result == null)
        {
            throw new ArgumentNullException(nameof(result));
        }

        if (range != null && rangeLength == 0)
        {
            return Task.CompletedTask;
        }

        if (range != null)
        {
            Logger.WritingRangeToBody();
        }

        return WriteFileAsync(context.HttpContext, result.FileStream, range, rangeLength);
    }

    /// <summary>
    /// Write the contents of the fileStream to the response body.
    /// </summary>
    /// <remarks>
    /// <para>If the file is not a <see cref="CallbackStream" />, it does the same thing as <see cref="FileStreamResultExecutor" />.</para>
    /// <para>If the file stream is a <see cref="CallbackStream" />, it will use <see cref="Stream.CopyToAsync(Stream, System.Threading.CancellationToken)" /> to write to the response body.</para>
    /// </remarks>
    /// <param name="context">The <see cref="HttpContext"/>.</param>
    /// <param name="fileStream">The fileStream to write.</param>
    /// <param name="range">The <see cref="RangeItemHeaderValue"/>.</param>
    /// <param name="rangeLength">The range length.</param>
    /// <returns>The async task.</returns>
    protected new static async Task WriteFileAsync(HttpContext context, Stream fileStream, RangeItemHeaderValue? range, long rangeLength)
    {
        // essentially, if our stream is the special stream we expect, use our custom way to sending the response to the client
        // else, do what you originally did
        if (fileStream is CallbackStream)
        {
            try
            {
                using (fileStream)
                {
                    await fileStream.CopyToAsync(context.Response.Body, context.RequestAborted);
                }
            }
            catch (OperationCanceledException)
            {
                context.Abort();
            }
        }
        else
        {
            await FileStreamResultExecutor.WriteFileAsync(context, fileStream, range, rangeLength);
        }
    }
}
```

### `LoggerExtensions.cs`
- in order to make the executor implementation match exactly, we need to duplicate the logger extension it access because the logger extension is marked as `internal` in aspnetcore
```cs
/// <summary>
/// This is a copy of https://github.com/dotnet/aspnetcore/blob/6c396e75d616674ba3b89e6cd44ce38c3b5244a1/src/Mvc/Mvc.Core/src/MvcCoreLoggerExtensions.cs#L1005 which is internal
/// </summary>
/// <remarks>
/// <para>This is so we can maintain the same implementation for the <see cref="DeferredPushFileStreamResultExecutor.WriteFileAsync" /> as the <see cref="FileStreamResultExecutor.WriteFileAsync" /> but only change the way we write the stream to the response body</para>
/// </remarks>
internal static class LoggerExtensions
{
    private static readonly Action<ILogger, Exception> _writingRangeToBody;

    static LoggerExtensions()
    {
        _writingRangeToBody = LoggerMessage.Define(
            LogLevel.Debug,
            new EventId(17, "WritingRangeToBody"),
            "Writing the requested range of bytes to the body...");
    }

    public static void WritingRangeToBody(this ILogger logger)
    {
        _writingRangeToBody(logger, null);
    }
}
```

### `CallbackStream.cs`
- because we break the Liskov Substitution Principle here, this isn't the prettiest solution, but we are in a situation where we need to choose our poison
- this uses the same "callback" philosophy as Stephen Clearly
```cs
/// <summary>
/// This is a stream that will only push contents to a stream passed into <see cref="CallbackStream.CopyToAsync(Stream, int, CancellationToken)" />
/// </summary>
/// <remarks>
/// <para>This intentionally breaks the Liskov Substitution Principle as there is no way to make functions like <see cref="Stream.Read(byte[], int, int)" /> for something that only writes directly to a stream</para>
/// <para>Any attempt to read or write to this stream will be met with an <see cref="NotImplementedException" /></para>
/// <para>This was created to be a stream so it can be used within the <see cref="FileStreamResult" /> architecture of aspnetcore with use of <see cref="DeferredPushFileStreamResultExecutor" /></para>
/// <para>This workaround will no longer be necessary when the following issue is fixed: https://github.com/dotnet/runtime/issues/1541</para>
/// </remarks>
/// <seealso cref="DeferredPushFileStreamResultExecutor" />
public class CallbackStream : Stream
{
    private Func<Stream, CancellationToken, Task> _streamLoader;

    /// <summary>
    /// Initializes a new <see cref="CallbackStream"/>.
    /// </summary>
    /// <param name="streamLoader">The callback called when the stream is called to write to a destination</param>
    public CallbackStream(Func<Stream, CancellationToken, Task> streamLoader)
        => _streamLoader = streamLoader;

    /// <inheritdoc />
    public override async Task CopyToAsync(Stream destination, int bufferSize, CancellationToken cancellationToken)
        => await _streamLoader(destination, cancellationToken);

    /// <summary>
    /// This is accessed by <see cref="FileStreamResultExecutor.ExecuteAsync" /> to determine if there is a length to write to the content length header
    /// So, we set it to false so it passes that check:
    /// https://github.com/dotnet/aspnetcore/blob/main/src/Mvc/Mvc.Core/src/Infrastructure/FileStreamResultExecutor.cs#L45
    /// </summary>
    public override bool CanSeek => false;

    // Fields we need to override because we inherit from Stream here
    public override bool CanRead => throw new NotImplementedException();

    public override bool CanWrite => throw new NotImplementedException();

    public override long Length => throw new NotImplementedException();

    public override long Position { get => throw new NotImplementedException(); set => throw new NotImplementedException(); }

    public override void Flush() => throw new NotImplementedException();

    public override int Read(byte[] buffer, int offset, int count) => throw new NotImplementedException();

    public override long Seek(long offset, SeekOrigin origin) => throw new NotImplementedException();

    public override void SetLength(long value) => throw new NotImplementedException();

    public override void Write(byte[] buffer, int offset, int count) => throw new NotImplementedException();
}
```

### `Startup.cs`
- we need to add our custom executor to the DI container so it overrides the one that is [automatically included in MVC](https://github.com/dotnet/aspnetcore/blob/39dccd7ca27389c12dfd877996afde55ff10fc00/src/Mvc/Mvc.Core/src/DependencyInjection/MvcCoreServiceCollectionExtensions.cs#L258)
```cs
public void ConfigureServices(IServiceCollection services)
{
    // ...
    services.AddTransient<IActionResultExecutor<FileStreamResult>, FileCallbackResultExecutor>();
    // ...
}
```

### `Controller.cs`
- as long as we use our `CallbackStream`, we can leverage all of the hard work put into making returning file streams easy
```cs
[ApiController]
[Route("/downloads")]
public class DownloadsController : ControllerBase
{
    private static HttpClient Client { get; } = new HttpClient();

    [HttpGet]
    public IActionResult Get()
    {
        var filenamesAndUrls = new Dictionary<string, string>
        {
            { "README.md", "https://raw.githubusercontent.com/StephenClearyExamples/AsyncDynamicZip/master/README.md" },
            { ".gitignore", "https://raw.githubusercontent.com/StephenClearyExamples/AsyncDynamicZip/master/.gitignore" },
        };

        return File(
            // the stream parameter will be the response body
            // the cancellationToken will be the HttpContext.RequestAborted cancellation token
            new CallbackStream(async (stream, cancellationToken) =>
            {
                using (var zipArchive = new ZipArchive(stream, ZipArchiveMode.Create))
                {
                    foreach (var kvp in filenamesAndUrls)
                    {
                        var zipEntry = zipArchive.CreateEntry(kvp.Key);
                        using (var zipStream = zipEntry.Open())
                        // pass along the cancellation token so we can stop the request if the request is cancelled
                        using (var stream = await Client.GetStreamAsync(kvp.Value, cancellationToken))
                        {
                            try
                            {
                                // pass along the cancellation token so we can stop zipping if the request is cancelled
                                await stream.CopyToAsync(zipStream, cancellationToken);
                            }
                            // handle the case that the request is aborted while we write to the response
                            catch (TaskCanceledException)
                            {
                                /// throws OperationCancelledException which is what we catch in the executor
                                cancellationToken.ThrowIfCancellationRequested();
                                // if the request isn't cancelled and we received a TaskCanceledException, then something is wrong
                                throw;
                            }
                        }
                    }
                }
                _logger.LogInformation("Finshed zip file download for {0}", info.FileName);
            }),
            "application/octet-stream",
            "MyZipfile.zip"
        );
    }
}
```

### Conclusion
- this implementation is not ideal, and neither is the one provided by Stephen Cleary
- each have their up sides and down sides
- in the end, choose the poison you are comfortable with
  - for me, I wanted to get the file streaming as close to the implementation already provided