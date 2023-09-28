While rewriting one of my web sites with ASP.NET I noticed that although support for the ASP.NET Membership Provider comes included out of the box in a ASP.NET MVC 2 project not all the options are fully implemented to the same extend that they are in a brand new ASP.NET WebForms project. For example, the option to reset your own password if you forgot your old one is not available out of the box in an ASP.NET MVC project.

Out of the box the following options are fully implemented in a ASP.NET MVC project:

* Create new user
* Login in
* Login out
* Change your password

**Adding support for Password Recovery to an ASP.NET MVC project** turned out to be pretty easy as the core functionality already exists in the Membership Provider and it's just a matter of calling it from your application.

**Update on November/2012** Please be aware that I wrote this blog post when *ASP.NET MVC 2* had just come out. The ideas on this blog post apply to newer ASP.NET MVC versions but the code will be slightly different. For example, the `MembershipService` on the `AccountController` referenced in this blog post is actually the `Membership` class referenced on `AccountController` in a default ASP.NET MVC 3 project. I have not looked to see what it will take to adapt this code for ASP.NET MVC 4, though.

The process that I implemented goes like this:

* From the `LogOn` view users can go to the `PasswordReset` view
* In the `PasswordReset` the user indicates his/her username and then they are sent to the `QuestionAndAnswer` view
* In the `QuestionAndAnswer` view the user enters the answer to their own security question
* Finally, the user is sent to the `PasswordResetFinal` view with a message indicating that their password has been reset and e-mailed to them.

The rest of this blog post describes the details on how this works. The source code is also available in the link at the bottom of this post.


## New Views and Controllers

### LogOn

The first thing that I did was update the `LogOn` view that comes with ASP.NET and added a link to start the Password Reset process. I wired this link to the a new method called `PasswordReset` in the `AccountController`.

![LogOn View](https://hectorcorrea.com/images/password_logon.JPG)
 

### Password Reset

Secondly I created the HTTP-GET `PasswordReset` method in the `AccountController` and a very simple view (`PasswordReset.aspx`) to allow the user to enter his/her username so that we can reset their password. The `PasswordReset.aspx` view is extremely simple as it only has a textbox where the user enter their user name.

![Password Reset View](https://hectorcorrea.com/images/password_reset.JPG)

```code
public ActionResult PasswordReset()
{
    if (!MembershipService.PasswordResetEnabled) 
    {
        throw new Exception("Password reset is not allowed");
    }
    return View();
}
```

I also implemented an HTTP-POST `PasswordReset` method in the `AccountController` to pick up the data and continue the process. This controller method decides whether the next step is to reset the password for this user or if we need to ask him/her a Password Recovery question before we reset their password. This step is required to honor the `requiresQuestionAndAnswer` configuration setting in the ASP.NET Membership Provider.


```code
[HttpPost]
public ActionResult PasswordReset(string userName)
{
    if (!MembershipService.PasswordResetEnabled) 
    {
        throw new Exception("Password reset is not allowed");
    }

    if (MembershipService.RequiresQuestionAndAnswer)
    {
        return RedirectToAction("QuestionAndAnswer", new { userName = userName } );
    }
    else
    {
        MembershipService.ResetPassword(userName, GetLoginUrl());
        return RedirectToAction("PasswordResetFinal", new { userName = userName });
    }
}
```


### Password Question and Answer

If the Membership Provider is configured to require a question and answer before resetting a user's password then we route users to the `QuestionAndAnswer` view. This view is also very simple as it merely has two labels (one with the username and another with password question for the user) and a textbox where the user will enter the answer to their password question.

![Security Question View](https://hectorcorrea.com/images/password_question.JPG)

To support this `QuestionAndAnswerView` I implemented an HTTP-GET controller method that fetches the security question for the username entered in the `PasswordReset` view.

```code
public ActionResult QuestionAndAnswer(string userName)
{
    if (!MembershipService.PasswordResetEnabled) 
    {
        throw new Exception("Password reset is not allowed");
    }
    ViewData["UserName"] = userName;
    ViewData["Question"] = MembershipService.GetUserQuestion(userName);
    return View();
}
```

Finally I added an HTTP-POST method to support the `QuestionAndAnswer`. By the time we get to this HTTP-POST method we have all the information that we need to reset a user's password (namely the user name and the answer to the security question.) Hence this method is where the call to actually reset the user's password actually happens.

```code
[HttpPost]
public ActionResult QuestionAndAnswer(string userName, string answer)
{
    if (!MembershipService.PasswordResetEnabled) 
    {
        throw new Exception("Password reset is not allowed");
    }
    MembershipService.ResetPassword(userName, answer, GetLoginUrl());
    return <b>RedirectToAction("PasswordResetFinal", new { userName = userName });</b>
}
```


### Password Reset Final

The last step in the process if a new view called `PasswordResetFinal` that just displays a message to the user telling him/her that a new password has been generated and e-mailed to them.

![Password Reset Final View](https://hectorcorrea.com/images/password_final.JPG)


## Changes to the Model

Every ASP.NET MVC project comes with a default model called `AccountMembershipService` to support the Views and Controllers that handle membership information. This `AccountMembershipService` is not much more than a wrapper for the ASP.NET `MembershipProvider`. Adding functionality to this model to support the password reset operation was very simple as the `MembershipProvider` already provides the core functions. The `MembershipService` referenced in the controller actions in the code actually point to an instance of this `AccountMembershipService`.

For example, the `QuestionAndAnswer` view calls the following method to to retrieve the security question for a user. Notice how this method's only job is to forward the calls to the `Membership` provider.

```code
public string GetUserQuestion(string userName)
{
    MembershipUser user = _provider.GetUser(userName, false);
    if (user == null)
    {
        throw new Exception("User name not found");
    }
    else
    {
        return user.PasswordQuestion;
    }
} 
```

All in all I added two methods to the `AccountMembershipService` (one to retrieve a user's security question and one to actually do the password reset)  plus a few properties to expose a couple of features of the Membership provider (like the need for a security question) that were not exposed on the default implementation.


## In Summary...

As I indicated at the beginning of this blog post, adding support for Password Recovery to an ASP.NET MVC project turned out to be pretty easy as the core of the functionality already exists in the `Membership` Provider.

You can **download the source code** of a brand new working ASP.NET MVC web site updated to support the password recovery functionality from [here](https://hectorcorrea.com/downloads/passwordrecoverymvc.zip). Keep in mind that this code assumes that you already have the ASP.NET membership provider and its database configured in your system. The `readme.txt` file inside the download provides further instructions on how to run this sample.