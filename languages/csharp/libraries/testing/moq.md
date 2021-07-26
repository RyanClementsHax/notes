# Moq

## [Moq - Non-overridable members may not be used in setup / verification expressions](https://stackoverflow.com/questions/56905578/moq-non-overridable-members-may-not-be-used-in-setup-verification-expression)
- moq creates an instance of the class if it is mocking out a concrete type
- any fields/methods it mocks MUST be overridable (i.e. virtual)
- please use interfaces if possible
  - it makes everything so much easier

## Mocking funcs
```cs
var mockCallback = new Mock<Func<Stream, CancellationToken, Task>>();
mockCallback.Setup(x => x.Invoke(It.IsAny<Stream>(), It.IsAny<CancellationToken>()))
    .Returns(Task.CompletedTask);
```
  - its simpler than you think
  - the trick is to mock out the `.Invoke` method

## Mocking HttpContext
- you can use mock objects
    ```cs
    // if you need to mock out the request object too
    var mockRequest = new Mock<HttpRequest>();
    mockRequest.SetupGet(x => x.Headers)
        .Returns(new HeaderDictionary());

    // if you need to mock out the response object too
    var stream = new MemoryStream();
    var mockResponse = new Mock<HttpResponse>();
    mockResponse.Setup(x => x.Body)
        .Returns(stream);
    mockResponse.SetupGet(x => x.Headers)
        .Returns(new HeaderDictionary());

    var token = new CancellationToken();

    var mockHttpContext = new Mock<HttpContext>();
    mockHttpContext.Setup(x => x.Request)
        .Returns(mockRequest.Object);
    mockHttpContext.Setup(x => x.Response)
        .Returns(mockResponse.Object);
    mockHttpContext.Setup(x => x.RequestAborted)
        .Returns(token);
    ```
- you can also instantiate a version of it yourself
    ```cs
    new DefaultHttpContext()
    {
        // for example if you want to stub out a field on it
        RequestServices = mockServiceProvider.Object
    }
    ```

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

## Verifying setters
- sometimes you want to verify that a property was set to something
- in this case, use `VerifySet` on the mock object
```cs
var value = "foo";
var mockMyObject = new Mock<MyObject>();
// ...
mockMyObject.VerifySet(x => x.SomeString = value);
```

## [Mock Behavior](https://github.com/Moq/moq4/wiki/Quickstart#customizing-mock-behavior)
- this configures the strictness of the mock
- setting to `Strict` will cause the mock to throw if a method/property wasn't explicitly mocked out

## Spies
- they aren't called this in the library, but we can instantiate an object that calls all the instance methods as normal, but we can assert on access and invokations
- just set `CallBase = true` when creating the mock
```cs
var mock = new Mock<IFoo> { CallBase = true };
```

## [LINQ to Mocks](https://github.com/Moq/moq4/wiki/Quickstart#linq-to-mocks)
- you can use a special syntax to define mock objects instead of creating everything from scratch
- its composable too
```cs
var services = Mock.Of<IServiceProvider>(sp =>
    sp.GetService(typeof(IRepository)) == Mock.Of<IRepository>(r => r.IsAuthenticated == true) &&
    sp.GetService(typeof(IAuthentication)) == Mock.Of<IAuthentication>(a => a.AuthenticationType == "OAuth"));

// Multiple setups on a single mock and its recursive mocks
ControllerContext context = Mock.Of<ControllerContext>(ctx =>
     ctx.HttpContext.User.Identity.Name == "kzu" &&
     ctx.HttpContext.Request.IsAuthenticated == true &&
     ctx.HttpContext.Request.Url == new Uri("http://moqthis.com") &&
     ctx.HttpContext.Response.ContentType == "application/xml");

// Setting up multiple chained mocks:
var context = Mock.Of<ControllerContext>(ctx =>
     ctx.HttpContext.Request.Url == new Uri("http://moqthis.me") &&
     ctx.HttpContext.Response.ContentType == "application/xml" &&
     // Chained mock specification
     ctx.HttpContext.GetSection("server") == Mock.Of<ServerSection>(config =>
         config.Server.ServerUrl == new Uri("http://moqthis.com/api")));
```