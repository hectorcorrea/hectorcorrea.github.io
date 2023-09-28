Below are the brief notes that I took while at DevConnections in Las Vegas (November/2008) This blog entry is mostly bullet points of things that I found interesting without much details. I'll elaborate on some of these topics in future blog entries.

## Tuesday, November 11th


### Opening Keynote

Scott Gu started with an overview of some of the new features released in the last year which included:

* Dynamic data access (to build a database application with a wizard
* Charting controls for ASP.NET
* ASP.NET MVC - TDD friendly, closer to the metal (HTML) programming model, Not for everyone
* Several Silverlight demos

Then he went to describe some of the new things coming in Visual Studio 2010 and ASP.NET 4.0 which included:

* Visual Studio's UI will use WPF rather than Windows Form
* Better support for release and deployment (e.g. support for multiple web.config files, one for each environment)
* Better support for documentation and integration with TFS work items
* Native support for SharePoint
* TDD workflow
* Distributed caching for ASP.NET apps (look for project Velocity)
* NET continuum (web, desktop, RIA)

Chris Hammond has a very nice summary of this session.

### Entity Framework in a World of Services and Processes

The Entity Data Model (.edmx) generated with the Entity Framework (EF) is serializable by default which makes it compatible with WCF out of the box.

Context in EF is the equivalent to a database connection in data access

Gotcha: You need to enumerate LINQ results before passing them back with the EF if you (as you should) surround your context with a using statement

```code
using (EF context)
{
  var blog = from x in ctx.Blogs select y;
  return blogs.ToArray() // enumerate before end of using
}
```

In my opinion, the Entity Framework seems unfinished. Although EF makes really easy to ready data using an object model the interface to save data (inserts and updates) looks complicated. Several "hacks" were needed to save data. The problem seems to stem from the fact that the EF is very good understanding state but not inferring intent. Currently all operations apply to the entire object graph and there is no easy way to make operations more granular. This will be worked out in version 2.

ADO.NET Data Sets (formerly known as Astoria) makes data accessible through HTTP via a REST approach. In this approach the intent is inferred from the URL.

SOAP uses lots of verbs while REST few verbs (put/get/post/delete) but lots of nouns.


### ASP.NET MVC - so what?

Scott Hanselman gave a very good presentation describing what MVC provides and does not when compared with traditional ASP.NET Web Forms.

The first thing he clarified is that ASP.NET is greater than MVC or WebForms. Secondly, both approaches are here to stay. They target different audiences.

ASP.NET with Web Forms hides HTML from the developer and adds a state subsystem to HTML.

MVC is a new project type in ASP.NET. It gives the developer more control over the HTML, lends to code easier to test (TDD friendly), but it is not for everyone. Unlike WebForms there are no event in MVC, that is there is no button’s click event, no page postback, no viewstate, and no page lifecycle.

If we lose so many “web form” features with MVC, isn’t it like going back to classic ASP? No, there was no separation of concerns in classic ASP (more on this below) the code was all lumped in a single page and it wasn’t easy to test.

You can mix and match WebForms and MVC in a single web site.

A typical URL in an MVC follows this format: http://controller/method/methodparameter, for example: http://products/retrieve/3

The name MVC comes from the Model View Controller pattern. Sites with MVC use a controller (C) to interact with a model (M) to get data and return views (V). This model enforces a clear separation of concerns between the model, the view, and the controller, for example, the controller does not know anything about HTML, that’s the job of the view. The view does not know anything about the database, that’s the job of the model.

The default view used by MVC uses WebForms. You can build your own or use third party views. Scott showed a kind of view using a third party product called NHAML that was quite different to a traditional WebForm way of constructing an HTML page.


### LINQ to everything

The speakers gave a tour of all the different LINQ flavors to query a variety of data sources. Slides can be found at http://blogs.msdn.com/aconrad/ 

* LINQ to objects - Provides a mechanism to query objects.
* LINQ to SQL – Provides LINQ’s functionality but targeting a database (SQL Server only) rather than objects.
* LINQ to Entities – A higher level of abstraction than LINQ to SQL, includes support for modeling (Entity Data Models) and supports multiple backend via plug-ins (although the only available at this point is for SQL Server)
* LINQ to DataSet – So that you don’t los you investment on DataSets Microsoft provides this flavor. The tag line is: "Not having to say you're sorry" Supports everything from DataSets (e.g. offline access) with a few extra features (e.g. LINQ joins)
* LINQ to XML – Provides a lightweight XML API (lighter than the DOM.) Several new XML classes are LINQ aware. For example, the new XDocument is LINQ aware but not the old XmlDocument.

Easier to generate XML documents with Linq to XML than traditional use of the DOM.

```code
XDocument xdoc = new XDocument(
new XElement("contacts", 
from c in contacts where country = "South Africa")
select new XElement("name")
```
 
VB.NET you can type XML directly as regular VB code (without having to add quotes) and embed expressions in the XML (similar to the way they are embedded in an ASP.NET) that get rendered at runtime to produce the final XML. This is really cool. Too bad we don’t have it in C#.

* LINQ to ADO.NET Data Services - ADO.NET Data Services allow you to publish your data model as REST-based web services so that any HTTP client can consume them. For example you can use a URL like this http:/localhost/myservice.svc/customers("ALFKI")/Orders to retrieve orders for customer with key “ALFKI”. You can query ADO.NET Data Services with LINQ.
* LINQ to Cloud Data Sources - At PDC earlier this year Microsoft unveiled two "cloud data sources": Microsoft Azure and SQL Data Services
* LINQ to (insert your custom provider here) – You can write your own data source and make it LINQ friendly. Must implement IQueryable<T> and IQueryProvider. The two main challenges are (1) translate LINQ expressions to your native data source language and (2) transform query results to objects. They demo a LINQ to Twitter implementation.


### Implementing RESTful Services with WCF 3.5 SP1

 www.RobBagby.com

In 1993 Web = Content, today Web = Content + Capabilities. Web is a graph of linked resources. Resources support a fixed set of operations (HTTP verbs)

REST is an architectural style, not a specification

Play well with HTTP (honor response codes, use verbs) as we build other applications to run on the web. HTTP has proven to be good why not continuing using it.

New 3.5 binding webHttpBinding does not use SOAP envelope, it's RESTful friendly.

Use WebGet and WebInvoke attributes.

WCF REST Starter Kit (CodePlex project) has three components: New DLL Microsoft.ServiceModel.Web.dll,  Visual Studio 2008 Templates and REST Samples.

Rob suggested four features our services should provide: (1) Use appropriated HTTP verbs, (2) Chose a representation, (3) Provide addressability, and (4) use valid HTTP response codes. These features are fully supported in the WCF REST Starter Kit via (1) WebGet attribute, (2) ResponseFormat parameter, (3) URITemplate, and (4) WebProtocolException class. 

 
## Wednesday, November 12th 


### From one server to two: Making the leap

I came late to this session so I don’t have much on the first part of it but I found it very interesting. The speaker (Richard Campbell) was very honest on what you get and what you don’t get when you start using more than one server to host your application.

You use load balancing to improve reliability and scalability, but that does not necessarily translate into performance. By using load balancing the application will be more resistant to hardware faults (reliability) and support more users (scalability) but it won’t become faster.

NLB adds overhead to the processing of a request, especially if you use server affinity.

You tell the cluster to drain a server, this will let the server finish processing whatever requests/sessions are currently on the server but it won’t take more requests. This work as expected when no affinity is used. When affinity is used draining a server can take hours since sessions don’t expire immediately and users could keep submitting requests on the same session and the machine won’t be free up until all sessions have been closed or expired.

If you use the no affinity approach objects need to be marked as serializable to be saved in the session cache. This is because with no affinity session is stored out of proc. It’s very important to keep session information to a minimal when storing it out of process.

Windows comes with a State Server that stores session (state) information in memory. It’s about twice as fast as storing information in SQL Server but it is a single point of failure. You can store session information in SQL Server as well and cluster SQL Server to prevent single point of failure. Third parties are worth considering.

He recommends that if you create a cluster, go all the way: use the no affinity approach.

When testing clusters make sure you test large time spans (10-15 minutes at least) to make sure you get a realistic picture.

Windows NLB detects if a machine goes down but it does not detect if IIS becomes non-responsive. You get what you paid for it.  


### WCF Durable Services

Juval Lowy started this session by talking about Custom Context in WCF and you can use this to pass out of band parameters in the header of a request. For example, if you need to have a way to identify who is making the call instead of passing an extra parameter on each method call you can store this information in the Context of the request and pass it back and forth.

There are three bindings that support this feature, look for bindings with the word “Context” on them, for example BasicHttpContextBinding.

He’s build a few wrapper classes that make using the context information a breeze. Download them from his site: http://idesign.com

After explaining this Juval moved to talk about durable sessions. He started by describing how in most client/server programming there is the implicit assumption that requests are short (milliseconds to minutes) Most client/server programming models assume this and work OK when the duration of the requests are short but fall short when requests take hours or days. On large time spans clients may come and go; hosts may come and go or move to different machines.

For long running requests you cannot keep service state in memory at all times, you need to persist it. Clients need to provide a state identifier so that the server can pull the correct state from the persistent storage. There are two basic was ways to pass this information around. One is to add an extra parameter to every call and pass the session identifier there but this is ugly and introduces “infrastructure” information to the business methods. A better approach is to pass this information in the context of the request.

You can add values to the context of a request from both, the client and the server. Server is the preferred approach.

If you change values on a static context on the server side make sure you make it thread safe (i.e. use lock)

To use durable services add DurableService attribute to your service class.

You can mark some of your methods with DurableOperation(CompleteInstance=true) to indicate that this method completes a session (e.g. the work to be done has been completed) and let the server know that when this method is called it can clear the session information for this caller. Once you end a session you need to recreate it (custom code required.)

Juval showed all his examples using a “File Persistent” provider to keep things simple. WCF provides a SQL Persistent provider. Look under the 3.5\SQL folder for the scripts to create the database.

Towards the end Juval closed the session by demoing “transactional memory” – this was very cool but I got lost in the process and my notes are not very clear. The basic idea was that you can implement transactions for operations that happen in memory. For example, execute this process but don’t change any values in memory if an exception happens in the process. Basically you decide when changes to values in memory are committed. 


### Building a WCF Router for Your Applications

This Michelle Leroux Bustamante talked how to create a WCF Service to route messages. There are two kinds of routers: Pass-Through routers and Processing Routers. This was by far the most advanced session that attended in the conference. Even though Michelle did a great job explaining the topic it was waaaaay more than I could understand.

She described several reasons why you might want to build your own router. A routing service can serve as a software load balancer (which is typically not very practical since routers are cheap these days) but it can also do routing based on the content of the message (requests from strict SLA clients go to the best servers) something that a hardware solution couldn’t provide. A router service is also useful if you want to support online/offline scenarios.

There are several considerations that you need to be aware when building a service router. When a router receives a message and forwards it to a different service it can change its header, content, both, or none. If a router A does not change the header information and forwards it to service A that does not use security the request will be accepted by service A. However if the Service A implements security at it will reject the message because in the header indicates that the message was originally for router A (not Service A.) If the router is smart enough it can change the header of the message to indicate that the receiving end is really Service A but this introduces another issue because now Service A will see that somebody has tampered with the original headers. Michelle explained ways to address these issues using supported WCF functionality. She even showed how to support different transport protocols between the client and the router (e.g. HTTP) and the router and the service (TCP.) None of this was trivial for the uneducated like me but nevertheless possible and well supported in WCF.

In general it seems that routing is very easy until you turn on security. It’s very easy to thing that it only took a couple of minutes to get it working until you realize that security is off. Once you turn security on then you’ll deal into the things that she described.

There were several interesting demos showing how messages would go back and forth to different servers depending on different algorithms including round robin, information on the message header or the message body.


### LINQ to SQL

Dino Esposito presented a very good introduction to LINQ to SQL. LINQ to SQL provides an object model on which LINQ operates to read and write data to a database. It also provides an abstraction layer on top of connections, data readers, datasets, transactions, and such.

LINQ to SQL is SQL Server specific. Other vendors might support it in the future.

Classes generated in the object model used by LINQ to SQL have C# attributes that describe what table/column they represent in the database. These classes are partial and you can extend them, for example to add business logic.

Relationships between tables are implemented as composite classes in the object model provided by LINQ to SQL. For example, Customer.Invoices[0].DueDate and Invoice.Customer.FirstName

The entry point to the object model is the DataContext class. This is the class that tracks changes. This class is short-lived and it’s recreated on each ASP.NET request. Opening a DataContext does not necessarily open a database connection, the database will not be hit until the first read or write to the data is detected. The fact that this class is recreated on each request is an issue in n-tier applications. This is the issue mentioned on the “Entity Framework in a World of Services and Processes” session yesterday and that will not be addressed until V2.0 of the Entity Framework.

Something that I found curious is that for queries you use LINQ syntax (which looks a lot like SQL syntax) but for updated you need to create a new object, populate the values that you want to change, and then call some specific methods (like SubmitChanges) to push the data to the database. Like in the session about the Entity Framework yesterday it seems that they’ve done a great job when it comes to reading data but the procedure to perform updates seems kludgy. That whole concept that you clone an object and then attached to a different DataContext just does not look right. I am sure you can get used to it but it just makes the product seem unfinished.

Dino was extremely honest on some of the awkward things when working with LINQ to SQL (most of which also apply to EF.) For example if you turn of lazy loading (aka deferred instantiation) some of the children collections like Customer.Orders.Count will return zero. He also showed some of the hacks that he had to go through when trying to force an update when a property that didn’t match one to one to a field was used.

In general LINQ to SQL and the Entity Framework look like great ideas that one should keep an eye on. If I didn’t knew about the CSLA Framework that Rockford Lhotka provides for free I would be all over this stuff, but since Lhotka’s framework has been around for a while is a more mature product. You can use LINQ (and LINQ to SQL and I suppose even the Entity Framework) with CSLA but CSLA provides enough that I don’t see much value on LINQ to SQL and EF in their current state. 


## Thursday, November 13th 


### High Performance Computing (HPC)

Nick Landry presented this session. High Performance Computing is used in scenarios when a user needs to submit a massive job, where as load balancing is used to handle massive number of users. An HPC Cluster is similar to a super computer, more than a typical network cluster.

An example where HPC is used is to calculate stock projections. Calculating the stock projection for a single stock is a very intensive job and it gets even worse when we need to calculate projections for the entire portfolio of a large client. In this case a massive job (calculate stock projections for these 100 stocks) is submitted to HPC cluster in which multiple machines work on the same job.

Microsoft provides a product called Windows HPC Server 2008 (formerly known as “Compute Cluster Server) that runs on top of Windows 2008 to create a HPC Cluster. http://microsoft.com/hpc

Version 1 of Windows HCP accepted batch jobs only submitted via a command line. In Version 2 there is an API that allows you to submit jobs.

To get started you need Windows Server 2008 plus the HPC Pack. The HPC Pack is licensed by CPU and Microsoft’s offer is the cheapest of the products around. The HPC Pack is what you use to setup the “head node” or to join an existing cluster. The head node runs the scheduler and knows about other machines.

There are .NET libraries to submit jobs to a HPC Cluster. For example, a single GetProjectionsForAllMyStocks() call will be broken down into multiple tasks (for example one per available computer) and run in parallel.  


### Productive WCF

In this session Juval Lowy focused on how to use WCF more than the plumbing underneath. He started by talking about WcfSvcHost.exe to host WCF Services without having to write your own host wrapper code. You can use it to start a WCF library project. You can call it with a /client parameter to fire both server and client applications at the same time. You can also call WcfTestClient.exe program to call a particular server (similar to the ASP.NET Web Service test page)

Instrumentation. WCF has several performance counters that you can use to monitor WCF Services. Just turn on performance counters in your config file (under ServiceModel) and monitor them with perfmon like you would typically would.

Tracing and logging. “It’s an illusion that clients care about the details of an exception” – most clients application have “if returnValue == OK then do X else display message to the user” In reality there is very little that the client application can do with the error other than telling the user “sorry” – if there was something to do the app would have already done it. Don’t create custom exception. Use the already build in ones.

You can turn on tracing and logging via the configuration file or with the WCF Configuration tool (look under diagnosis.) Make sure you turn on “auto-flush” so that the logging information is saved even if you stop the debugger in the middle of a session.

The “activity ID” checkbox in the WCF Configuration tool refers to a “logical thread” that tracks the activity across all tiers (client, host, service, et cetera.) The TraceViewer utility can automatically merge the log file from the client and the server with this activity ID. This is really cool.

Operations Overloading. You  can modify the proxy class to support multiple method overloading by tagging them different via an attribute. For example to alias Add(int, int) into AddInt and Add(float, float) into AddFloat.

DataSets and DataTables. You should not return DataSets in your services. They are not good for other platforms other than .NET and are bound to database schema. You should use arrays instead

Fault Debugging. By default exception information is not bubbled up to the client application. He showed some code on the server side where he throws exception using throw new FaultException(ExceptionDetail) where ExceptionDetail is a custom class. You need to turn on “include exception detail” at the service level for this to work.

Once you turn use this code you can catch FaultException<ExceptionDetail> in addition to a generic catch FaultException in your client application. 


### Using jQuery with ASP.NET

Rick Strahl gave a very good overview of what is jQuery, what it can do, and how to use it.

jQuery is conceptually like “LINQ to HTML” in the sense that it allows you to query the HTML document at runtime and returns collections of objects. Rick called it the new HTML API.

jQuery is open source and quite popular, there are 100s of plug-ins available for it. The download is pretty small: 16KB. It uses CSS 3 selector syntax (dot to select classes, pound sign to select IDs)