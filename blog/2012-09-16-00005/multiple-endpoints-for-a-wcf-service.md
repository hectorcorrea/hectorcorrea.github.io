# Multiple Endpoints for a WCF Service
One of the nice features that Windows Communication Foundation (WCF) provides is the ability to expose a single service via multiple endpoints so that different client applications can consume the same service over different network protocols. For example the same service can be exposed via HTTP for external clients and via TCP for internal clients. 

In WCF an **endpoint** refers to the combination of the address where service is available, the binding used to communicate to it, and the contract that the service exposes, these three parameters are commonly known as the "ABC" of the endpoint.

* The **address** represents the URL where the service is to be found, for example `http://localhost:8000/HelloWorld/HelloWorldService` or `net.tcp://localhost:9000/HelloWorld/HelloWorldService`
* The **binding** describes the transport protocols for the communication channel, for example HTTP vs TCP
* The **contract** represents the functionality that the service exposes and it maps to a `ServiceContract` interface in your C# code.

An endpoint for a WCF Service can be configured via the `app.config` of the service or through code. In this post I am going to focus on how to configure a service via the `app.config` file to be available to multiple endpoints.


## The Service

So let's assume we have a simple "Hello World" WCF service that implements `IHelloWorld` interface like the one shown in the following code snippet.

```code
[ServiceContract(Namespace="https://hectorcorrea.com/wcf")]
public interface IHelloWorld
{
    [OperationContract] string Greetings(string yourName);
    [OperationContract] DateTime TheTimeIs();
}
 
public class HelloWorldService : IHelloWorld
{
    public string Greetings(string yourName)
    {
        return string.Format("Hello {0}", String.IsNullOrEmpty(yourName) ? "Guest" : yourName);
    }
 
    public DateTime TheTimeIs()
    {
        return DateTime.Now;
    }
}
```

To host this service you can use the following code. The first line creates a host object for the `HelloWorldService` and the second line opens a communication channel to receive and process requests for this service. The call to `host.Open()` in this example will read the settings from the `app.config` (more on this later) to decide what endpoints will be opened for the service. The rest of the code displays what endpoints were found in the app.config for this service and finally we use `ReadLine` to put the host in a wait state and let it process requests as they come.

```code
ServiceHost host = new ServiceHost(typeof(TheService.HelloWorldService));
host.Open();
Console.WriteLine("Host has been started");
Console.WriteLine("Endpoints");
foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
{
    Console.WriteLine("Address: {0}", endpoint.Address);
    Console.WriteLine("Binding: {0}", endpoint.Binding);
    Console.WriteLine("Contract: {0}", endpoint.Contract.ContractType);
    Console.WriteLine();
}
 
Console.WriteLine("Please [ENTER] to close the service host...");
Console.ReadLine();
host.Close(); 
```


## Server Side Configuration

The following XML shows how the different endpoints for the `TheService.HelloWorldService` are defined in the `app.config`. This is the config file that lives on the *server side*. Notice the three endpoints for the contract `TheService.IHelloWorld`, these endpoints expose the service via basic HTTP binding, TCP binding, and WS binding and each of them has its own URL.

```code
<service behaviorConfiguration="TheServiceBehavior" name="TheService.HelloWorldService">
  <!-- endpoint 1: Basic HTTP -->
  <endpoint address="http://localhost:8000/HelloWorld/HelloWorldService"
            binding="basicHttpBinding" name="httpEndpoint" 
            contract="TheService.IHelloWorld" />
  <!-- endpoint 2: TCP -->
  <endpoint address="net.tcp://localhost:9000/HelloWorld/HelloWorldService"
            binding="netTcpBinding" bindingConfiguration="" name="tcpEndpoint"
            contract="TheService.IHelloWorld" />
  <!-- endpoint 3: WS HTTP -->
  <endpoint address="http://localhost:8003/HelloWorld/HelloWorldService"
            binding="wsHttpBinding" bindingConfiguration="" name="wsHttpBinding"
            contract="TheService.IHelloWorld" />
  <host>
    <timeouts openTimeout="00:30:00" />
  </host>
</service>
```


## On the Client Side

Now that we have a WCF `HelloWorld` service implemented and a host running waiting for requests to be send let's turn to the client side and create a simple console application to consume it.

The following code will connect to the `HelloWorld` service via a proxy class called `HelloWorldClient` using the endpoint named `tcpEndpoint`. It then makes a couple of calls to the `Greetings` and `TheTimeIs` methods in the service and finally closes the connection.

```code
HelloWorldClient service = new HelloWorldClient("tcpEndpoint");
Console.WriteLine("Greetings: {0}", service.Greetings("Hector"));
Console.WriteLine("The time is: {0}", service.TheTimeIs());
service.Close();
Console.WriteLine("Channel closed.");
Console.WriteLine(); 
```

In this code the class `HelloWorldClient` is a proxy for the `HelloWorld` service that we created before. The process to generate this proxy class is beyond the scope of this post (see references at the bottom of this post for more information.) In a nutshell, this proxy is a class that implements a skinny version of the `HelloWorldService` that uses WCF to forward the request across the wire to the actual server. Thanks to WCF we don't have to write any of the low level networking stuff to deal with HTTP or TCP since WCF wraps that functionality for us.

This proxy class makes the code in the client application looks like its calling a class running on the same machine as the client. By just looking at this code, you have no idea if the call to `service.Greetings` is to a class running on the same machine as the client or in a different machine across the country.

So how do we tell the client where to find the real `HelloWorld` service? As with the server side settings you can do it either via code or using a config file. In this post I am going to show how to do it using a config file (keep in mind that this client side config file is different config from the server side config file that we discussed before.)


## Client Side Configuration

Like the server side configuration file that we reviewed before, the client side configuration also has endpoints and, as you can imagine, they are subset of the endpoints that the server exposes. *The client side config tells the client application where to find the service while the server side config tells the server where to listen for requests.*

The following XML shows how the endpoints are defined in the client side config. In this particular example we have all three endpoints for the `HelloWorldService` listed for illustration purposes, but we could have as well just one. You will notice that the definition of these endpoints is similar to the ones that we used for the server config file as they also have an address, a binding, and a contract. Yet, keep in mind that this is a different config file -- this config defines client side settings.

```code
<client>
  <!-- endpoint 1: Basic HTTP -->
  <endpoint address="http://localhost:8000/HelloWorld/HelloWorldService"
            binding="basicHttpBinding" bindingConfiguration="httpBindingParameters"
            contract="HelloWorldProxy.IHelloWorld" name="httpEndpoint" />
  <!-- endpoint 2: TCP -->
  <endpoint address="net.tcp://localhost:9000/HelloWorld/HelloWorldService"
            binding="netTcpBinding" bindingConfiguration="tcpBindingParameters"
            contract="HelloWorldProxy.IHelloWorld" name="tcpEndpoint" />
  <!-- endpoint 3: WS HTTP -->
  <endpoint address="http://localhost:8003/HelloWorld/HelloWorldService"
            binding="wsHttpBinding" bindingConfiguration="wsHttpBindingParameters"
            contract="HelloWorldProxy.IHelloWorld" name="wsHttpEndpoint" />
</client>
```

You will notice that the name (`tcpEndpoint`) of the second endpoint in this XML matches with the name of the endpoint that we indicated in the line 

```code
HelloWorldClient service = new HelloWorldClient("tcpEndpoint");
```

When this line is executed WCF will read the values of the `tcpEndpoint` from the client config file and open a TCP communication channel at the address indicated to communicate with a service that recognizes messages for the `HelloWorldService` contract.

After this the calls to `service.Greetings()` and `service.TheTimeIs()` are automatically forwarded to the real service. If our service is up and running and listening for request on the same endpoint (i.e. same address, binding, and contract) it will process the request and return the expected result.

You could change the first line to use the HTTP endpoint by just changing the name of the endpoint:

```code
HelloWorldClient service = new HelloWorldClient("httpEndpoint");
```

That's it, with one little change the client application will now use HTTP instead of TCP to send requests to the server but *the rest of the code in the client application remains unchanged*. As I mentioned at the beginning a typical scenario is to use TCP for internal clients and HTTP for external clients.


### References

A great book on WCF and where I learned most the information in this blog post is [Learning WCF](http://www.amazon.com/Learning-WCF-Hands-Michele-Bustamante/dp/0596101627) by Michelle Leroux Bustamante

If you want more information on how to create the proxy class used on the client two good references are [this MSDN link](http://msdn.microsoft.com/en-us/library/ms733133.aspx) and Miguel Castro's article on [how to do it manually](http://www.devx.com/codemag/Article/39837/0/page/1).