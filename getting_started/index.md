# Getting Started
Welcome, and it’s nice to know you want to learn %DarkRift! This tutorial aims to be a comprehensive guide for anyone who wants to get started with %DarkRift no matter how much networking experience you have. Nevertheless, as someone who hates oversimplification, I’ll try and keep it interesting for people who do have networking experience!

As a tutorial, we’re going to recreate a popular game called [Agar.io](http://agar.io). We won’t implement all the features it has but we’ll get a basic system of moving and eating going and you can always add the others as an exercise afterwards.
      
## Completed Project
The completed tutorial project is available on GitHub in 2 parts: The [Unity project](https://github.com/DarkRiftNetworking/AgarUnityComplete) and [the plugin](https://github.com/DarkRiftNetworking/AgarPluginComplete).

# Minimal Example

If instead you are impatient (like I sometimes am) and just want to see a full code embedding %DarkRift into your own host process, here is one.

First, we start a server that gets its settings from the local file server.config.

```csharp
using DarkRift;
using DarkRift.Server;
using System.Collections.Specialized;

ServerSpawnData spawnData = ServerSpawnData.CreateFromXml("Server.config", new NameValueCollection());

var server = new DarkRiftServer(spawnData);

void Client_MessageReceived(object? sender, MessageReceivedEventArgs e)
{
    using Message message = e.GetMessage();
    using DarkRiftReader reader = message.GetReader();
    Console.WriteLine("Received a message from the client: " + reader.ReadString());
}

void ClientManager_ClientConnected(object? sender, ClientConnectedEventArgs e)
{
    e.Client.MessageReceived += Client_MessageReceived;

    using DarkRiftWriter writer = DarkRiftWriter.Create();
    writer.Write("World of Hel!");

    using Message secretMessage = Message.Create(666, writer);
    e.Client.SendMessage(secretMessage, SendMode.Reliable);
}

server.ClientManager.ClientConnected += ClientManager_ClientConnected;

server.StartServer();

Console.ReadKey(); // Wait until key press. Not necessary in Unity.
```

The XML file server.config looks like this (hard to make shorter).

```xml
<?xml version="1.0" encoding="utf-8" ?>
<!--
  Configuring DarkRift server to listen at ports TCP 4296 and UDP 4297.
-->
<configuration xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:noNamespaceSchemaLocation="https://www.darkriftnetworking.com/DarkRift2/Schemas/2.3.1/Server.config.xsd">
  <server maxStrikes="5" />
  
  <pluginSearch/>
 
  <logging>
    <logWriters>
      <logWriter name="ConsoleWriter1" type="ConsoleWriter" levels="trace, info, warning, error, fatal">
        <settings useFastAnsiColoring="false" />
      </logWriter>
    </logWriters>
  </logging>

  <plugins loadByDefault="false"/>

  <data directory="Data/"/>

  <listeners>
    <listener name="DefaultNetworkListener" type="BichannelListener" address="0.0.0.0" port="4296">
      <settings noDelay="true" udpPort="4297" />
    </listener>
  </listeners>
</configuration>

```

And finally, here is a client that connects to the server and sends "Hello world!" whilst receiving a string that should be "World of Hel!" (just be mindful about pressing any key since that terminates the program early).

```csharp
using DarkRift;
using DarkRift.Client;
using System.Net;

var client = new DarkRiftClient();

void Client_MessageReceived(object? sender, MessageReceivedEventArgs e)
{
    using Message message = e.GetMessage();
    using DarkRiftReader reader = message.GetReader();
    Console.WriteLine("Received a message from the server: " + reader.ReadString());
}

client.MessageReceived += Client_MessageReceived;

client.Connect(IPAddress.Loopback, tcpPort:4296, udpPort: 4297, noDelay:true);

Console.WriteLine("Connected!");

using DarkRiftWriter writer = DarkRiftWriter.Create();
writer.Write("Hello world!");

using Message secretMessage = Message.Create(1337, writer);
client.SendMessage(secretMessage, SendMode.Reliable);

Console.ReadKey(); // Wait until key press. Not necessary in Unity.
```

Do note that "Connected!" message can be printed even after "World of Hel!" since DR2 is multithreaded.

This was an example of embedding %DarkRift into your own programs. You can instead choose to implement DarkRift.Server.Plugin (see the manual) for looser coupling.