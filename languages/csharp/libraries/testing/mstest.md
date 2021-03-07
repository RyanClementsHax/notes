# MSTest

## Test lifecycle
1. Collect all test cases
1. Split collection into two based on DoNotParallel flag.
1. Group parallel collection based on ExecutionScope flag.
1. Create queue of groups
1. Create threads where each
    1. Take first from queue
    1. Check assembly initialize execution and run in case is not executed yet
    1. Check class initialize execution and run in case is not executed yet
    1. Execute test method
1. Wait for all threads
1. Run all not parallel tests
1. Run all class cleanups
1. Run assembly cleanup
- if you write your tests independent of each other, you will never need to concern yourself with this
- all non parallel tests run after parallel tests
- the class cleanup function is not called until ALL tests, both parallel and sequential, are finished
  - there is a [github issue](https://github.com/microsoft/testfx/issues/580) for this (that contains my first comment on an OSS repo :D)
  - this means that if classes need to release resources for other tests to use (like db connections), they won't get released until all tests are finished

## [CollectionAssert.AreEqual vs CollectionAssert.AreEquivalent](https://stackoverflow.com/questions/29757258/collectionassert-areequivalent-vs-assert-equals#:~:text=AreEqual%20check%20that%20they%20have,same%20items%2C%20in%20any%20order.)
- the first does a strict equal of the elements pairwise
- the latter basically does set comparision

## Parameterized tests
- the simplest way is to add a `DataRow(...)` attribute to your test
- generating test data using a method source
  ```cs
  public static IEnumerable<object[]> GetData()
  {
      yield return new object[] { 1, 1, 2 };
      yield return new object[] { 12, 30, 42 };
      yield return new object[] { 14, 1, 15 };
  }

  [DataTestMethod]
  [DynamicData(nameof(GetData), DynamicDataSourceType.Method)]
  public void Test_Add_DynamicData_Method(int a, int b, int expected)
  {
      var actual = MathHelper.Add(a, b);
      Assert.AreEqual(expected, actual);
  }
  ```
- generating test data using a method source from another class
  ```cs
  // this can also be in a different file
  public static class OtherClass
  {
      public static IEnumerable<object[]> GetData()
      {
          yield return new object[] { 1, 1, 2 };
          yield return new object[] { 12, 30, 42 };
          yield return new object[] { 14, 1, 15 };
      }
  }

  [DataTestMethod]
  [DynamicData(nameof(GetData), typeof(OtherClass), DynamicDataSourceType.Method)]
  public void Test_Add_DynamicData_Method(int a, int b, int expected)
  {
      var actual = MathHelper.Add(a, b);
      Assert.AreEqual(expected, actual);
  }
  ```
- permuting test data
  - there is probably an way to do this [using attributes](https://www.meziantou.net/mstest-v2-customize-test-execution.htm)
  - for now this is what I've got
  - first we need a permute function
    ```cs
    public static IEnumerable<object[]> Permute(this IEnumerable<object[]> source)
    {
        IEnumerable<object[]> emptyProduct = new[] { Array.Empty<object[]>() };
        return source
            .Aggregate(
                emptyProduct,
                (accumulator, sequence) =>
                    from accseq in accumulator
                    from item in sequence
                    select accseq.Concat(new[] { item }).ToArray()
            );
    }
    ```
  - then we need a data generator method that generates a list of possible param values for each param
    ```cs
    public static IEnumerable<object[]> GetData()
        => new List<object[]>
        {
            new object [] { 1, 1, 2 }, // all possible values for first param
            new object [] { 12, 30, 42 } // all possible values for second param
            // ...
        }.Permute();
    ``` 
  - the test takes in params like normal
    ```cs
    [DataTestMethod]
    [DynamicData(nameof(GetData), DynamicDataSourceType.Method)]
    public void Test_Add_PermutedDataSource_Method(int a, int b /* ... */)
    {
        // ...
    }
    ```
  - notes
    - this allows for non-static data to be generated as opposed to a solely attribute based solution that can only take in static values
    - if you need related data to be passed through (like in asserting a certain input results in a certain output)
      - first consider if permuting your test data is what you want
      - second if you need some related data, but permute all other values do the following
        - make one of your params to your test a tuple of all the related values your test uses
        - configure your test data to have a list of tuples that have all of this related data
        ```cs
        public static IEnumerable<object[]> GetData()
            => new List<object[]>
            {
                // some param values to permute,
                new [] { ("hello", "hola"), ("goodbye", "adios") }
                // some param values to permute
            }.Permute();

        [DataTestMethod]
        [DynamicData(nameof(GetData), DynamicDataSourceType.Method)]
        public async Task Test_Add_PermutedDataSource_Method(/* some params */ (string, string) englishSpanishTranslation /* other params */)
        {
            var (english, spanish) = englishSpanishTranslation;

            var result = translatorService.Translate(english, new CultureInfo("es-MX"));

            Assert.AreEqual(spanish, result);
        }
        ```
        - it might be tempting to implement this using dictionaries, but note that dictionaries in C# don't support null keys which might be something you want here
    - you can make some nice optimizations by handling a common case of only one value being permuted (but this has downsides that make it not recommended by me as you will see)
      - you can modify your permute function to be an extension of `IEnumerable<object>` and to convert everything that is not an `object[]` into an `object[]` of one value
      - this also handles single values that can be casted to `object[]` like in the case of `List<string>` because `x is object[]` matches specifically to `object[]`
        ```cs
        public static IEnumerable<object[]> Permute(this IEnumerable<object> source) // note the type change
        {
            IEnumerable<object[]> emptyProduct = new[] { Array.Empty<object>() };
            return source
                .Select(x =>
                    x is object[]
                        ? (object[])x
                        : new[] { x }
                )
                .AsEnumerable()
                .Aggregate(
                    emptyProduct,
                    (accumulator, sequence) =>
                        from accseq in accumulator
                        from item in sequence
                        select accseq.Concat(new[] { item }).ToArray()
                );
        }
        ```
      - your test can then look something like this
        ```cs
        public static IEnumerable<object[]> GetData()
            => new List<object> // note the type change
            {
                1, // this will be permuted with everything below it as if it were: new [] { 1 }
                new [] { 1, 1, 2 }, // all possible values for second param
                new [] { 12, 30, 42 } // all possible values for third param
                // ...
            }.Permute();

        [DataTestMethod]
        [DynamicData(nameof(GetData), DynamicDataSourceType.Method)]
        public void Test_Add_PermutedDataSource_Method(int a, int b, int c)
        {
            // ...
        }
        ```
      - you now have to be careful, however, when declaring param lists that the type it resolves to is what you expect
        ```cs
        public static IEnumerable<object[]> GetData()
            => new List<object>
            {
                // this type is (string, string)[] which is not object[] so this will be treated as a single param unless you convert it
                new [] { ("hello", "hola"), ("goodbye", "adios") },
                // this will be treated like a list of params with two values to be permuted
                new object [] { ("hello", "hola"), ("goodbye", "adios") },
                // same with this
                new object [] { ("hello", "hola"), ("goodbye", "adios") }.Select(x => (object)x).ToArray()

            }.Permute();
        ```
          - because of these edge cases, I wouldn't recommend implementing a permutation system like this
          - maybe there is a clean way to get around this, but I'm not sure

    - note that sometimes your data cannot be shared between tests like in the case of sharing objects that get disposed at the end of the test method
      - in this case, pass in funcs to generate this on the fly
        ```cs
        public static IEnumerable<object[]> GetData()
            => new List<object[]>
            {
                new object [] { 1, 1, 2 }, // all possible values for first param
                new object [] { 12, 30, 42 }, // all possible values for second param
                new Func<IDisposableObject>[] { () => new DisposableObject1(), () => new DisposableObject2() }
            }.Permute();

        [DataTestMethod]
        [DynamicData(nameof(GetData), DynamicDataSourceType.Method)]
        public void Test_Add_PermutedDataSource_Method(int a, int b, Func<DisposableObject> disposableObjectFactory)
        {
            using var disposableObject = disposableObjectFactory();
            // ...
        }
        ```
      - if you are using the single param simplification shown earlier, note that the permute function written there will read `Func<IDisposableObject>` as a single param and wrap it in its own `object[]`
        - to get around this, you can modify the type inline
          ```cs
          public static IEnumerable<object[]> GetData()
              => new List<object>
              {
                  new [] { 1, 1, 2 }, // all possible values for first param
                  new [] { 12, 30, 42 }, // all possible values for second param
                  new Func<IDisposableObject>[] { () => new DisposableObject1(), () => new DisposableObject2() }.Select(x => (object)x).ToArray();
              }.Permute();
          ```
        - or you can modify the permute function to make an exception
          ```cs
          public static IEnumerable<object[]> Permute(this IEnumerable<object> source)
          {
              IEnumerable<object[]> emptyProduct = new[] { Array.Empty<object>() };
              return source
                  .Select(x =>
                      x is object[] or Func<object>[] // add Func<object>[] to the exceptions list
                          ? (object[])x
                          : new[] { x }
                  )
                  .AsEnumerable()
                  .Aggregate(
                      emptyProduct,
                      (accumulator, sequence) =>
                          from accseq in accumulator
                          from item in sequence
                          select accseq.Concat(new[] { item }).ToArray()
                  );
          }
          ```
            - I think this solution might have some edge cases in the type system that depending on what type you return from your factory func, it might not be assignable to `Func<object>` or the array you create might not be assignable to `Func<object>[]`. In which case, stick to the first method (unless you have a better solution? #imAllEars)
