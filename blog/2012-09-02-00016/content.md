A few weeks ago I noticed that when users of my site request a blog topic that does not exist although I was displaying a user friendly message indicating that the topic does not exist **I was not returning the proper HTTP status code (404) to indicate to the user that the page was not found**. This approach is typically not a problem if the user visiting my site is a human (users don’t really care about HTTP status codes) but it is important if the “user” visiting the site is a web crawler like the Googlebot or the Yahoo Slurp because **for these non-human users the HTTP code is very important as it means something concrete to them**.

In digging deeper into this issue I realized that I was doing what is known as a “soft 404” which is not good for web crawlers because it fools them into thinking that there is a valid page for an invalid URL. In my case my site was returning a status code 200 (which means OK) rather than a status code 404 that means “Not Found.” This is in fact so common that both, the Googlebot and the Yahoo Slurp, web sites talk about this problem [here](http://www.google.com/support/webmasters/bin/answer.py?answer=181708) and [here](http://help.yahoo.com/l/us/yahoo/search/webcrawler/indexing-12.html).


## The Root of the Problem

The code that was causing this issue in my controller looked similar to this:

```code
public class BlogController : Controller
{
  public ActionResult Sample1(string topicName)
  {
    bool isTopicFound = SomeLogic(topicName);
    if (!isTopicFound)
    {
      var route = new RouteValueDictionary(new { controller = "Error", action = "NotFound" });
      return new RedirectToRouteResult(route);
    }
    // regular code to display topic
  }
}
```

Although this code displays a nice user friendly page to the user (via the NotFound action of the Error controller) because I am using `RedirectToRouteResult` the browser gets an HTTP code 302 (Found) followed by a 200 (OK) which is not what I wanted. Below is a trace of a request like this with Fiddler. Notice how the original request to `/Blog/Sample1` got a 302 response code indicating that something was found at a new URL and then the new URL (`/Error/NotFound`) in turn got an HTTP status code of 200 (OK) because this second URL indeed returned OK.

![HTTP code 302 plus HTTP code 200](https://hectorcorrea.com/images/http_302_200.jpg)


## Throwing an HttpException

When I decided to update my ASP.NET MVC site to handle this issue correctly my first thought was to throw an `HttpException` from my controller, for example something along these lines:

```code
public ActionResult Sample2(string topic)
{
  bool isTopicFound = false; // SomeLogic(topicName);
  if (!isTopicFound)
  {
    throw new HttpException(404, "Blog topic not found");
  }

  // regular code to display topic
}
```

The `HttpException` can either be handled in the controller’s `OnException` method or on the `Application_Error` (in Global.asax.) Whatever code we use we need to be sure we don't rely on `RedirectToRouteResult` like in the previous example or otherwise we'll be back on the HTTP 302 + HTTP 200 trap.

If the `HttpException` is not handled at all in the code then the `customErrors` section in the `web.config` could redirect the user to a default error page so they see a user friendly error message. In this case however the returned HTTP code is an HTTP 302 followed by an HTTP 200 just like in the previous example! This actually makes sense giving that the parameter in the `customErrors` section where the page is configured is clearly named `defaultRedirect`. However this does *not* address the problem that I am trying to solve.


## The ASP.NET MVC way

As it turns in ASP.NET MVC you can **directly change the status code of the response and still display whatever view you want to**. The code is actually very simple as shown in the following example. Notice how the status code is forced to 404 (Not Found) and the “Error” view is selected.

```code
public ActionResult Sample3(string topic)
{
  bool isTopicFound = false; // SomeLogic(topicName);
  if (!isTopicFound)
  {
    HttpContext.Response.StatusCode = 404;
    HttpContext.TrySkipIisCustomErrors = true; //Prevents the standard IIS 404 page from showing. 
    HttpContext.Response.Clear();
    return new ViewResult() { ViewName = "Error" };
  }
  // regular code to display topic
}
```

This code accomplishes both goals: the user sees a human friendly error (via the Error view) and the HTTP status code returned is 404 as show in the following Fiddler trace. Notice how the original request to `/Blog/Sample3` returns a 404 code directly without doing a redirect.

![HTTP code 404](https://hectorcorrea.com/images/http_404.JPG)

Line #7 in the previous example (`HttpContext.TrySkipIisCustomErrors = true;`) is required to hint IIS that we want to use our own HTTP 404 page rather than the IIS default page.

Now when somebody tries to access a topic that does not exist on my blog they get both a nice error message and the correct HTTP status code. ~~You can try this by hitting this link to a http://www.hectorcorrea.com/blog/a-non-existing-topic and looking with Fiddler the HTTP status code returned.~~ As of October/2012 this blog is using an entire different code base not using ASP.NET MVC. The information is this blog post is still accurate, though.


## A Couple of Gotchas

While looking into this problem I noticed that throwing an `HttpException` from a controller is slightly different than throwing any other exception. For example, although you can decorate your controllers with the `HandleError` attribute and configure what view will be rendered in case of exceptions, `HandleError` does not handle `HttpExceptions`. This [post on StackOverflow](http://stackoverflow.com/questions/812235/error-handling-in-asp-net-mvc/812344#812344) has more information on this issue.

The second gotcha that I ran into is that if you return an HTTP status code 404 and your view is smaller than 513 bytes Google's Chrome browser will not render your view but rather Chrome will render its own view. This issue has been [documented before](http://perso.hirlimann.net/~ludo/blog/archives/2008/09/chrome-and-404s.html) and it only happens on Chrome. Most likely this won't be an issue for you in production as most views are in fact larger than 512 bytes that by the time all the content of your page is considered but you should keep it in mind while testing as it might throw you off.