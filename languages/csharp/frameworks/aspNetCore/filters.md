# Filters

- these are "middleware" for controller actions
- like aspnet middleware, the request propagates them in order and the response propagates them in reverse order
- like aspnet middleware, they can alter the request and response objects
- unlike middleware, they have access to the action context (context of the action that the request was routed to)
- unlike middleware, they run in between all other middleware and when the action is called

## Filter pipeline

1. authorization
2. resource before
3. model binding
4. action before
5. action execution
6. action after
7. exception
8. result before
9. result execution
10. result after
11. resource after

## Filter types

- authorization
  - not recommended to write your own auth filter
  - all the built in one does is call the auth policies/providers/schemes, so look to do your work there
  - these only see requests comming in, not the response going out
  - they will short circuit the request if authorization fails
- resource
  - runs before model binding
  - good for things like
    - caching
    - disabling form data from being put in the model (like on file uploads)
- action
  - surrounds the execution of action methods
  - happens after model binding
  - good for things like
    - handling request transactions
    - model validation
- exception
  - error handling
  - do not catch exceptions that occur in resource filters, result filters, or MVC result execution
  - prefer middleware for error handling
  - only use these filters if you want to handle errors differently based on the action being executed
    - i.e. return html if the request was for a page, but return json for api based actions
- result
  - only executed when an action filter or action produces a result
  - not executed when
    - authorization filter or resource filter short circuits the pipeline
    - exception filter handles an exception by producing an action result
  - can short circuit other result filters and action result execution by cancelling
    - write to the response when doing this to avoid sending an empty response
  - throwing an exception prevents the execution of the action result and subsequent filters
    - is treated as a failure instead of a successful result
  - if you need to always run a result filter regardless of exception, you can use the following types: `IAlwaysRunResultFilter` and `IAsyncAlwaysRunResultFilter`
    - runs for all action results including action results produced by:
      - authorization filters and resource filters that short-circuit
      - exception filters

## Filter scopes

- the following levels run in order of
  - [controller method overrides](https://docs.microsoft.com/en-us/aspnet/core/mvc/controllers/filters?view=aspnetcore-5.0#controller-level-filters)
    - every controller has `OnActionExecuting` and `OnActionExecuted` methods you can override
    - these run before any other filters

    ```cs
    public class TestController : Controller
    {
        [SampleActionFilter(Order = int.MinValue)]
        public IActionResult FilterTest2()
        {
            return ControllerContext.MyDisplayRouteInfo();
        }

        public override void OnActionExecuting(ActionExecutingContext context)
        {
            // Do something before the action executes.
            MyDebug.Write(MethodBase.GetCurrentMethod(), HttpContext.Request.Path);
            base.OnActionExecuting(context);
        }

        public override void OnActionExecuted(ActionExecutedContext context)
        {
            // Do something after the action executes.
            MyDebug.Write(MethodBase.GetCurrentMethod(), HttpContext.Request.Path);
            base.OnActionExecuted(context);
        }
    }
    ```

  - global
  - controler level filter attribute
  - action

## [Order](https://docs.microsoft.com/en-us/aspnet/core/mvc/controllers/filters?view=aspnetcore-5.0#overriding-the-default-order)

- within a filter type, filters are first sorted by order, then scope is used to break ties

## [Dependency Injection](https://docs.microsoft.com/en-us/aspnet/core/mvc/controllers/filters?view=aspnetcore-5.0#dependency-injection)

- because attributes are instantiated when applied to their targets, there is no opportunity to pass in a DI context to their constructors
- if you need DI in the attribute, use one of the special types provided (see link)
  - they have edge cases you need to consider though
- you can also use a [filter factory](https://docs.microsoft.com/en-us/aspnet/core/mvc/controllers/filters?view=aspnetcore-5.0#ifilterfactory)
  - this is the preferred way

## [Using middleware in the filter pipeline](https://docs.microsoft.com/en-us/aspnet/core/mvc/controllers/filters?view=aspnetcore-5.0#using-middleware-in-the-filter-pipeline)

- this will make the middleware a resource filter
