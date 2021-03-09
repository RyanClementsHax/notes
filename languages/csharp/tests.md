# Tests

## Property count equals
```cs
/// <summary>
/// Asserts that he given type has exactly a number of properties.
/// </summary>
/// <param name="count">The number of properties expected</param>
/// <param name="type">The type under test</param>
public static void PropertyCountEquals(int count, Type type)
{
    var numProperties = type.GetProperties().Length;
    if (numProperties != count)
    {
        // It is important we fail the test, because otherwise if we use Assert.Inconclusive(msg), I have found that this goes unnoticed in practice
        Assert.Fail($"The type {type} has a different number of properties than expected. Expected: {count}. Actual: {numProperties} This test requires updating to ensure that all of the fields are mapped appropriately.");
    }
}
```

## Collection assert extensions
```cs
/// <summary>
/// Calls .ToList() on both of the IEnumerables first then checks to make sure the lengths are equal,
/// then lastly asserts that each item of the first list matches the corresponding one in the other list
/// according to the function given in
/// </summary>
/// <typeparam name="T">Type of the items in the expected enumerable</typeparam>
/// <typeparam name="U">Type of the items in the actual enumerable</typeparam>
/// <param name="expected">The expected enumerable</param>
/// <param name="actual">The actual enumerable</param>
/// <param name="assertItemsEqual">The lambda passed in that asserts on each pair of items</param>
public static void AssertEqualTo<T, U>(this IEnumerable<T> expected, IEnumerable<U> actual, Action<T, U> assertItemsEqual)
{
    if(expected == null && actual == null)
    {
        return;
    }
    else if (expected == null)
    {
        Assert.Fail("The expected enumerable is null while the actual enumerable wasn't");
        return;
    }
    else if (actual == null)
    {
        Assert.Fail("The actual enumerable is null while the expected enumerable wasn't");
        return;
    }

    var expectedList = expected.ToList();
    var actualList = actual.ToList();
    Assert.AreEqual(expected.Count(), actual.Count());
    for(int i = 0; i < expected.Count(); i++)
    {
        assertItemsEqual(expectedList[i], actualList[i]);
    }
}

/// <summary>
/// Asserts on each item in the enumerable given after calling .ToList()
/// </summary>
/// <typeparam name="T">Type of the items</typeparam>
/// <param name="enumerable">The enumerable that this extension method applies to</param>
/// <param name="assertion">A lambda that asserts on each item in the enumerable</param>
public static void AssertAll<T>(this IEnumerable<T> enumerable, Action<T> assertion)
    => enumerable.ToList().ForEach(x => assertion(x));
```

## Writing a large test using docker compose
- refer to [Large Tests Using Docker](../../../docker/largeTestsUsingDocker.md) for more resources
1. create a large test
1. create a docker file that runs the test
    - the base image should be the sdk you are using instead of the runtime env (ex: `mcr.microsoft.com/dotnet/sdk:3.1`)
    - the entrypoint should be the test command with a [filter](https://docs.microsoft.com/en-us/dotnet/core/testing/selective-unit-tests?pivots=mstest) for the test/tests you want to run
    ```dockerfile
    FROM mcr.microsoft.com/dotnet/sdk:3.1
    COPY . /app
    WORKDIR /app
    RUN dotnet build
    ENTRYPOINT ["dotnet", "test", "--no-build", "--filter", "FullyQualifiedName=namespace.to.your.test.class.test_name"]
    ```
      - you can leave out the `RUN dotnet build` and `--no-build` flag if you want the build to happen when you run the container
2. create a docker compose that starts your service using the docker file you created
    ```yml
    services:
      db:
        image: mcr.microsoft.com/mssql/server:2019-latest
        environment:
          ACCEPT_EULA: "Y"
          SA_PASSWORD: "$DB_PASSWORD"
      your_service:
        build:
          context: .
          dockerfile: your-large-test.Dockerfile
          image: your-large-test # can be whatever
        depends_on:
          - db
    ```
3. run the following commands to run your test
    ```bash
    docker-compose -f your-large-test.docker-compose.yml up --build
    ```
4. preferrably leave a comment on the large test and/or in the README with instructions on how to run this test

## Mocking `HttpClient`
- [ref](https://gingter.org/2018/07/26/how-to-mock-httpclient-in-your-net-c-unit-tests/)
```cs
// ARRANGE
var handlerMock = new Mock<HttpMessageHandler>(MockBehavior.Strict);
handlerMock
   .Protected()
   // Setup the PROTECTED method to mock
   .Setup<Task<HttpResponseMessage>>(
      "SendAsync",
      ItExpr.IsAny<HttpRequestMessage>(),
      ItExpr.IsAny<CancellationToken>()
   )
   // prepare the expected response of the mocked http call
   .ReturnsAsync(new HttpResponseMessage()
   {
      StatusCode = HttpStatusCode.OK,
      Content = new StringContent("[{'id':1,'value':'1'}]"),
   })
   .Verifiable();
 
// use real http client with mocked handler here
var httpClient = new HttpClient(handlerMock.Object)
{
   BaseAddress = new Uri("http://test.com/"),
};
 
var subjectUnderTest = new MyTestClass(httpClient);
 
// ACT
var result = await subjectUnderTest
   .GetSomethingRemoteAsync('api/test/whatever');
 
// ASSERT
result.Should().NotBeNull(); // this is fluent assertions here...
result.Id.Should().Be(1);
 
// also check the 'http' call was like we expected it
var expectedUri = new Uri("http://test.com/api/test/whatever");
 
handlerMock.Protected().Verify(
   "SendAsync",
   Times.Exactly(1), // we expected a single external request
   ItExpr.Is<HttpRequestMessage>(req =>
      req.Method == HttpMethod.Get  // we expected a GET request
      && req.RequestUri == expectedUri // to this uri
   ),
   ItExpr.IsAny<CancellationToken>()
);
```