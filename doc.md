
**C Shrap TCP eventbus client** is the Vert.x TCP eventbus client module for C Sharp. I hope you are familiar with Vert.x TCP eventbus bridge. If not get more details from [here](http://vertx.io/docs/vertx-tcp-eventbus-bridge/java/ "http://vertx.io/docs/vertx-tcp-eventbus-bridge/java/"). 

- [Open project on Github](https://github.com/jaymine/TCP-eventbus-client-C-Sharp)
- [Download module](https://www.nuget.org/packages/VertxEventbus/1.2.0-beta)

## Implementation ##

Under Eventbus, 2 threads are running for input and output streams.

![Complete Design](https://raw.githubusercontent.com/jaymine/TCP-eventbus-client-Python/gh-pages/2.png)

TCP eventbus bridge has 2 special features

- LV messages 
- Json schema

![LV Message](https://raw.githubusercontent.com/jaymine/TCP-eventbus-client-Python/gh-pages/3.png)

Every message consists of a json message and the length of the json message. Length of a json message is an unsigned integer. It means 4 bytes. But the module is done that work for you. You just have to focus on the json message.


### Create Eventbus ###

to import eventbus,

```cs	
using io.vertx; 
```

to create eventbus,

```cs	
Eventbus eb = new Eventbus("127.0.0.1",7000,1000); 
```
or 

```cs	
Eventbus eb = new Eventbus("127.0.0.1",7000); 
```

Eventbus constructor takes 3 arguments

- Host - default is "127.0.0.1"
- Port - default is 7000
- Time Out - Default is 1000 milliseconds. Socket is using polling method to establish an asynchronous environment. Time out is polling time period. 

### Addressing ###

Every eventbus message has an address and the client will handle the message according to its address. An address can be any string.

```cs	
eg: "vertx", "pink pig", "x.y.z" 
```

### JObject ###

Here Newtonsoft.json has been used.

to import,

```cs	
using Newtonsoft.Json.Linq; 
```

to create JObject,
```cs
	JObject body_sub =new JObject();
    body_sub.Add("message","sub");
```
for more info go to [Newtonsoft.Linq](http://www.newtonsoft.com/json/help/html/QueryingLINQtoJSON.htm)

### Handling Messages ###

To handle a message, Handlers are used. Handlers are structures that take 2 arguments. 

to implement a handler,
```cs
	Handlers myhandler=new Handlers(
        "Get",//can change into any address
        new Action<JObject>(
             message =>
                      {
                    	//here your code comes
                      }
        )
    );
```

A Handler takes 2 argument 

- address - string
- Action function - It takes an argument JObject. 
	

### Register Handlers ###

In order to handle messages, Handlers should be registered under an address.

to register a handler,
```cs
	eb.register("Get",myhandler);
```
 Register under the same address.

After that any message that comes to the address will be sent to the handler.

### Unregister Handlers ###

to unregister an address,
```cs
eb.unregister("Get");
```

### Headers ###

Headers is the structure that handles headers for each message. 

to create,
```cs
Headers h = new Headers();
h.addHeaders("type", "maths"); 
```

- Headers.addHeaders(key, value) - add headers to message
- Headers.deleteHeaders() - delete all headers from message
- Headers.getHeaders() - returns JObject of Headers


### Send ###

Messages can be sent in 2 ways.
```cs
		eb.send(
                "Get",//address
                body_add,//body
                "Get",//reply address
                h, //headers
                (new ReplyHandlers("Get",//replyhandler address
                   new Action<bool, JObject>( //replyhandler function
                       (err, message) =>
                       {
                   		//your code comes here
                       }
                   )
                )
               ),
               5);//timeinterval - Optional

		eb.send("Get", body_add, "Get", h);
        eb.send("Get", body_add, null, h);
```
Here timeInterval is Optional. Default is 10 seconds. ReplyHandler waits timeinterval for a reply. If the reply didnot come, then ReplyHandler will be called with an error (Timeout error).

### ReplyHandlers ###

ReplyHandlers are used to handle reply messages if there is a reply.

to create,
```cs
	ReplyHandler myRH=new ReplyHandlers("Get",//replyhandler address
                   new Action<bool, JObject>( //replyhandler function
                       (err, message) =>
                       {
                    if (err == false)
                               //your code comes here
                       }
                   )
                )
```
### Publish ###

Messages can be published in this way,
```cs
eb.publish("Get", body_close, h);
```

### Close Connection ###

You must close the connection end of the program. If not program wont close at the end.
```cs
eb.CloseConnection(5);
```

CloseConnection(time) - time in seconds. After that connection will be closed.

### Errors ###

Error messages will be write into error_log_.txt file.  

## Examples ###

You can get examples from Github

* [Simple example with the code](https://github.com/jaymine/TCP-eventbus-client-C-Sharp/tree/master/examples "Get client and server codes")

