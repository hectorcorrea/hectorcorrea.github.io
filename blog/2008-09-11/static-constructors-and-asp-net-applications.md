# Static constructors and ASP.NET applications
A few days ago I was running into an issue with a static class in an ASP.NET application. The issue (I thought) was that the static constructor was not being fired. It turns out the static constructor was being fired as it should but not as often as I thought it would be.

Static constructs are fired once per App Domain. 

In a *Windows application* this means that the constructor is fired once every time I launch the application and access the static class. If I close the application, run it again, and hit the static class the constructor will be fired again.

In a *Web application* that is also the case, but that does not translate to every time I close and re-open the browser. Closing the browser and re-launching it does not terminate the application and re-launches it.

Here is a simplified example of the scenario that I ran into, notice the `_count = 0` in the constructor:

```code
public static class StaticClass 
{ 
    static int _count; 
    static StaticClass() 
    { 
        _count = 0; 
    } 
    public static int Count 
    { 
        get 
        { 
            _count++; 
            return _count; 
        } 
    } 
} 
  
public partial class _Default : System.Web.UI.Page 
{ 
    protected void Page_Load(object sender, EventArgs e) 
    { 
        // print the count 
        lblText.Text = string.Format("Count={0}""", StaticClass.Count); 
    } 
}
```

The very first time I run this code the count prints 1. If I refresh the page it prints 2. **If I close the browser and run the application again, what would you expect it would print?**

If you guessed 1 you guessed wrong (like I did.) It will print 3.

Closing the browser does not get rid of the static class since apparently the App Domain still is running under IIS. If you really want to force the constructor of the static class to be fired (and in this case reset the counter to 0) you need to kill the worker process in IIS (e.g. run `iisreset`)

In hindsight this is obvious and, as one of my co-workers pointed out, we are lucky ASP.NET apps work like that! It would suck big time if I every time a user closed their browser the application would be closed as well and the next user will need to pay the load time.

The moral of the story is keep in mind that you don't have much control on *when* the static constructor will be fired in an ASP.NET application. You have the guarantee that it will be fired the very first time the class is accessed in the application domain. This includes the very first time the class is accessed after the app is loaded into IIS, but it also means that if the worker process is recycled (and your app domain goes away with it,) the static constructor will be fired (again) the next time the class is hit when a new user visits your site. This is all good and the desired effect, but just keep it in mind.