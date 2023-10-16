# AJAX calls with jQuery in ASP.NET MVC
One of the nice things about ASP.NET MVC is the ability to easily integrate standard web libraries like jQuery in .NET web applications. In this blog post I am going to show how to execute AJAX calls with jQuery from an ASP.NET MVC application. The first example shows how to do it with an **HTTP GET** call and the second one uses an **HTTP POST** call. Both examples use JSON in the return type.


## HTTP GET Example

This is probably the simplest example on how to execute an AJAX call with jQuery but it should be OK to show the general concept. In this page when the user clicks on the "click here" hyperlink a request is made to the server to retrieve the time on the server and display to the user. The AJAX part of this example is that the request to the server is executed *without* making a full page reload as it can be seen in the screenshot below (notice that the time the page was originally loaded does not change after I clicked the hyperlink and retrieved the server time.) Again, not terribly exciting but good enough for our purposes.

![Ajax Sample 1 screenshot](https://hectorcorrea.com/images/AjaxSample1Screen.jpg)


### The MVC View

The MVC view that makes this possible is rather simple and it's composed of the following elements

* A SCRIPT tag on the HEAD to include the jQuery library
* A SCRIPT block to declare the code to execute the jQuery call
* The HTML to render the page

To include jQuery in an ASP.NET MVC you just need to a add a `SCRIPT` tag to the head that looks like this:

```code
<head id="Head1" runat="server">
<title>AJAX Sample 1</title>
<script src="<%: Url.Content("~/Scripts/jquery-1.4.1.js") %>" type="text/javascript"></script>
</head>
```

The HTML code for this page is also very simple as we are only displaying some text coming from the server (line 2), rendering a hyperlink to let the user invoke the AJAX call, and rendering a placeholder to display the value returned by the AJAX call. Notice that there is no JavaScript code in this HTML block. This is by design and I'll describe the reason for this in the next paragraphs (see Hijax section below.)


```code
<h2>Ajax Sample 1</h2>
<p>Page loaded at: <%: System.DateTime.Now.ToString() %></p>
<p><a href="GetServerTime" id="AjaxLink">Click here</a> to get current time.</p>
<div id="CurrentServerTimeDiv"></div>
```

Of course the interesting part of this example is the actual JavaScript block that wires the HTML anchor tag to JavaScript and makes the AJAX call.

```code
<script type="text/javascript">
  $(document).ready(function () {
    //Setup the AJAX call
    $("#AjaxLink").click(function (event) {
      event.preventDefault();
      GetCurrentServerTime();
    });
  });

  function GetCurrentServerTime() {
    // Make the call to the server
    $.getJSON("GetServerTime", null, UpdateServerTime);
  }

  function UpdateServerTime(result) {
    // Update the page with the result
    var message = "Time on the server is: " + result.ServerTime;
    $("#CurrentServerTimeDiv").html(message);
    }
</script>
```

This JavaScript block has three main sections. The first section wires the HTML anchor tag with name `AjaxLink` to a JavaScript function called `GetCurrentServerTime()`. It is customary to add this setup code to the ready event to make sure it's executed when the DOM of the page has been fully constructed even if not all of the resources for the page (e.g. images) have been downloaded. You can read more about this [here](http://api.jquery.com/ready/). In this case we are replacing the `click` event of the anchor tag `AjaxLink` with a call to our own GetCurrentServerTime() JavaScript function.

The second section of the JavaScript block is the actual JavaScript function that will be executed when the user clicks on the link. This code executes a jQuery's `getJSON` call to URL `GetServerTime`, passing no arguments (`null`), and executing function `UpdateServerTime` upon completion. When this code is executed our MVC controller will be hit. More on this later.

The third section of the JavaScript code is executed when our controller finishes its execution. In our case the controller returns an object with one property `ServerTime` that we use to construct a message to display on the placeholder `CurrentServerTimeDiv` that was defined in the HTML code.


### The MVC Controller

When the user clicks on the hyperlink a request is sent through an HTTP GET to our controller. The controller will perform an operation (in this case just merely calculating the time on the server) and return this value to the view. The code for this controller is shown below:

```code
// GET /AjaxSample/GetServerTime
public ActionResult GetServerTime()
{
  var viewModel = new Models.AjaxSampleViewModel();
  viewModel.ServerTime = DateTime.Now.ToString();

  if (Request.IsAjaxRequest())
  {
    return Json(viewModel, JsonRequestBehavior.AllowGet);
  }

  // Fallback view if JavaScript is not supported by the browser
  return View("ServerTime", viewModel);
}
```

When this code executes through an AJAX call (as in our case) it will return a JSON object to the client with the results. This is why our JavaScript code was able to use `result.ServerTime` to display the result. The following screenshot shows how the resulting object looks in JavaScript:

![Ajax JSON result](https://hectorcorrea.com/images/AjaxSample1JSON.jpg)


### Hijax

You might be wondering why do we need a fallback clause in our controller if the view explicitly makes an AJAX call. The reason for this precaution is in case the client browser does not support JavaScript or JavaScript is disabled. Although JavaScript is almost always enabled in most desktop and laptop computers that's not always the case in mobile devices.

This is also the reason we wired the AJAX call on the document ready event rather than directly on the HTML anchor tag in our view. For example, although we could have just as easily wired the `AjaxLink` anchor tag directly to the `GetCurrentServerTime` function that wouldn't have worked if JavaScript was disabled. Instead we wired the JavaScript call on the document ready event which is only executed if JavaScript is enabled. If JavaScript is disabled (and therefore the document ready event is not fired) the HTML anchor tag in our view is already wired to hit the `GetCurrentServer` URL which maps to the `GetServerTime` method in our controller.

This approach to JavaScript is known as [Hijax](http://domscripting.com/blog/display/41) and is a good way to provide progressive enhancement to your web pages.


## HTTP POST Example

You can also execute AJAX calls using an HTTP POST request. For example, in the following screenshot the information in the textboxes (e-mail and comment) is sent through an asynchronous HTTP POST to the server.

![Ajax Sample 2 screenshot](https://hectorcorrea.com/images/AjaxSample2Screen.jpg)


### The MVC View

Like in our previous example, the MVC view that makes this possible is very simple and it's composed of the following elements

* A SCRIPT tag on the HEAD to include the jQuery library
* A SCRIPT block to declare the code to execute the jQuery call
* The HTML to render the page

The HTML code for this page is shown below. Notice how we declare an HTML form named `SaveCommentForm` with two textbox controls and a submit button. Below the form there is a placeholder section called `NewComment` where we will display the results from the server after the user has added a new comment. Like in our previous example there is no JavaScript on this HTML code, we will be wire the JavaScript call when the web page is rendered on the browser using the Hijax method that we described before. 

```code
<p>Page loaded at: <%: System.DateTime.Now.ToString() %></p>
<% using (Html.BeginForm("SaveComment", "AjaxSample", FormMethod.Post, new { id = "SaveCommentForm" })) { %>
  Your e-mail address:<%: Html.TextBox("emailAddress")%><br />
  Enter a comment:<br />
  <%: Html.TextArea("comment")%><br />
  <input type="submit" value="Save" />
<% } %>
<div id="NewComment"> </div>
```

The JavaScript that powers this web page is very similar to the one that we used in our previous example and is shown below.

```code
<script type="text/javascript">
  $(document).ready(function () {
    //Setup the AJAX call
    $("#SaveCommentForm").submit(function (event) {
      event.preventDefault();
      SaveComment(this);
    });
  });

  function SaveComment(form) {
    // Make an AJAX call to the server
    // notice that we request a JSON response
    $.ajax({
      url: form.action,
      type: form.method,
      dataType: "json",
      data: $(form).serialize(),
      success: CommentSaved
    });
  }

  function CommentSaved(result) {
    // Update the page with the result
    var newComment = "Thank you for you comment " + result.emailAddress + "<br/><pre>" + result.comment + "</pre>";
    var comments = newComment + "<br/>" + $("#NewComment").html();
    $("#NewComment").html(comments);
  }
</script>
```

The first section of this JavaScript block uses the Hijax technique aforementioned to redirect the submit event of the `SaveCommentForm` to call a JavaScript function named `SaveComment`.

The `SaveComment` function is defined on the second section of the JavaScript block. In this example we are issuing an HTTP POST to the original location that the HTML form would have on its own but we are making the call asynchronous (by using the AJAX function) and requesting that the result is delivered to us in JSON notation. When the asynchronous call completes we want jQuery to fire the `CommentSaved` function. The following picture shows the values that are passed at runtime to the `url`, `type`, and `data` parameters in this AJAX call. Notice that the `form.method` is POST and `form.action` points to the `SaveComment` URL.

![Ajax Sample 2 runtime values](https://hectorcorrea.com/images/AjaxSample2AJAX.jpg)

The third section of the JavaScript code is executed when our controller finishes its execution. In our case the controller returns a JSON object (similar to the HTTP GET example) that we use to update the page.


### The MVC Controller

The controller to process our HTTP POST request is shown below. In essence this code is very similar to the one that we used before in the HTTP GET example. We process the request (although in this code I just indicate that we would need to do something with it) and then return a JSON object back to the called. Like in our previous example we return a full view as a fallback plan.

```code
// POST /AjaxSample/SaveComment
[HttpPost]
public ActionResult SaveComment(string comment, string emailAddress)
{
  //[Code to actually save the comment would go here]
  if (Request.IsAjaxRequest())
  {
    return Json(new { comment = comment, emailAddress = emailAddress, savedAt = DateTime.Now.ToString() });
  }

  // Fallback view if JavaScript is not supported by the browser
  return View("CommentSaved");
}
```


## ...and that's it

As you can see it is relatively straightforward to make AJAX calls using jQuery within ASP.NET MVC views to your controllers.


### References

I took the idea of using Hijax for the jQuery calls from the book [ASP.NET MVC in Action](http://www.amazon.com/ASP-Net-MVC-Action-Jeffrey-Palermo/dp/1933988622) by Jeffrey Palermo et al. (pages 195-214)

In addition to the online documentation for jQuery, Joe Brinkman's e-book [jQuery for ASP.NET Developers](http://www.wrox.com/WileyCDA/WroxTitle/jQuery-for-ASP-NET-Developers.productCd-0470478454.html) is a great place for beginners. If you are new to jQuery this is probably the best way to spend 7 bucks (other than buying a latte and a croissant at your local coffee shop!)

Steve Sanderson's book [Pro ASP.NET MVC 2 Framework](http://www.amazon.com/ASP-NET-Framework-Second-Experts-Voice/dp/1430228865) also has a good explanation on how to use jQuery in ASP.NET MVC applications (pages 517-559) that is worth checking out.