# Timer

- time an async action
    ```cs
    private static async Task<long> TimeActionAsync(Func<Task> a)
    {
        var w = new System.Diagnostics.Stopwatch();
        w.Start();
        await a();
        w.Stop();
        return w.ElapsedMilliseconds;
    }
    ```
- time an async func
    ```cs
    private static async Task<(T, long)> TimeFuncAsync<T>(Func<Task<T>> a)
    {
        var w = new System.Diagnostics.Stopwatch();
        w.Start();
        T ret = await a();
        w.Stop();
        return (ret, w.ElapsedMilliseconds);
    }
    ```