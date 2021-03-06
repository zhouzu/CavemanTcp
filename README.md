﻿![alt tag](https://github.com/jchristn/cavemantcp/blob/master/assets/icon.ico)

# CavemanTcp

[![NuGet Version](https://img.shields.io/nuget/v/CavemanTcp.svg?style=flat)](https://www.nuget.org/packages/CavemanTcp/) [![NuGet](https://img.shields.io/nuget/dt/CavemanTcp.svg)](https://www.nuget.org/packages/CavemanTcp) 

CavemanTcp gives you the ultimate control in building TCP-based applications involving clients and servers.  

With CavemanTcp, you have full control over reading and writing data.  CavemanTcp is designed for those that want explicit control over when data is read or written or want to build a state machine on top of TCP.

Important:
- If you are looking for a package that will continually read data and raise events when data is received, see SimpleTcp: https://github.com/jchristn/simpletcp
- If you are looking for an all-in-one package that handles delivery of well-formed application-layer messages, see WatsonTcp: https://github.com/jchristn/watsontcp

## Disconnection Handling

Since CavemanTcp relies on the consuming application to specify when to read or write, there are no background threads continually monitoring the state of the TCP connection (unlike SimpleTcp and WatsonTcp).  Thus, you should build your apps on the expectation that an exception may be thrown while in the middle of a read or write.

## New in v1.1.0

- Breaking changes; read now returns ReadResult and write now returns WriteResult

## Examples

### Server Example
```
using CavemanTcp;

// Instantiate
TcpServer server = new TcpServer("127.0.0.1", 8000, false, null, null);
server.Logger = Logger;

// Set callbacks
server.ClientConnected += (s, e) => 
{ 
    Console.WriteLine("Client " + e.IpPort + " connected to server");
    _LastClient = e.IpPort;
};
server.ClientDisconnected += (s, e) => 
{ 
    Console.WriteLine("Client " + e.IpPort + " disconnected from server"); 
}; 

// Start server
server.Start(); 

// Send [Data] to client at [IP:Port] 
WriteResult wr = server.Send("[IP:Port]", "[Data]");

// Receive [count] bytes of data from client at [IP:Port]
ReadResult rr = server.Read("[IP:Port]", [count]);

// List clients
List<string> clients = server.GetClients().ToList();

// Disconnect a client
server.DisconnectClient("[IP:Port]");
```

### Client Example
```
using CavemanTcp; 

// Instantiate
TcpClient client = new TcpClient("127.0.0.1", 8000, false, null, null);
client.Logger = Logger;

// Set callbacks
client.ClientConnected += (s, e) => 
{ 
    Console.WriteLine("Connected to server"); 
};

client.ClientDisconnected += (s, e) => 
{ 
    Console.WriteLine("Disconnected from server"); 
};

// Connect to server
client.Connect(10);

// Send data to server
WriteResult wr = client.Send("Hello, world!");

// Read [count] bytes of data from server
ReadResult rr = client.Read([count]);
```

## WriteResult and ReadResult
```WriteResult``` and ```ReadResult``` contains a ```Status``` property that indicates one of the following:

- ```ClientNotFound``` - only applicable for server read and write operations
- ```Success``` - the operation was successful
- ```Timeout``` - the operation timed out (reserved for future use)
- ```Disconnected``` - the peer disconnected

```WriteResult``` also includes:

- ```BytesWritten``` - the number of bytes written to the socket.

```ReadResult``` also includes:

- ```BytesRead``` - the number of bytes read from the socket.
- ```DataStream``` - a ```MemoryStream``` containing the requested data.
- ```Data``` - a ```byte[]``` representation of ```DataStream```.  Using this property will fully read ```DataStream``` to the end.

## Help or Feedback

Need help or have feedback?  Please file an issue here!

## Version History

Please refer to CHANGELOG.md.

## Thanks

Special thanks to VektorPicker for the free Caveman icon: http://www.vectorpicker.com/caveman-icon_490587_47.html
