# LINQ

## Async lambdas
- doesn't work out of the box with linq, but [you can write wrappers](https://stackoverflow.com/questions/36445257/linq-and-async-lambdas)
    ```cs
    public static class AsyncExtensions
    {
        public static async Task<bool> AnyAsync<T>(
            this IEnumerable<T> source, Func<T, Task<bool>> func)
        {
            foreach (var element in source)
            {
                if (await func(element))
                    return true;
            }
            return false;
        }
    }

    //...

    static async Task MainAsync(string[] args)
    {
        int[] test = new[] { 1, 2, 3, 4, 5 };

        if (await test.AnyAsync(async i => await TestIt(i))
            Console.WriteLine("Contains numbers > 3");
        else
            Console.WriteLine("Contains numbers <= 3");
    }
    ```