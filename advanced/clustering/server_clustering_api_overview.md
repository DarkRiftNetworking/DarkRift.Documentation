# Server Clustering API Overview
Once a server is part of a cluster it becomes possible to communicate with other members of the cluster. This is primarily achieved using the remote server manager which will be discussed in brief here.

## Remote Server Manager
The `IRemoteServerManager` interface is a logical equivalent of the `IClientManager` interface for remote servers. It provides an API to access `IRemoteClient` objects (which are the logical equivalent of an `IClient`) and also to get details about the current server. Unlike the client manager however, the server manager provides the servers in via their respective server groups.

Remote servers are only accessible in the remote server manager if the server group they belong to is connected with the server group of the current server. Additionally, servers in the server group of the current server are not accessible.

The following code is written from the perspective of a plugin.

### Accessing Servers
In most cases you will access servers by knowing what group they're in.
```c#
IServerGroup serverGroup = RemoteServerManager.GetServerGroup(groupName);
IRemoteServer[] remoteServers = serverGroup.GetAllRemoteServers();
```

If the ID of a server is known then it can be accessed by performing a search of all groups.
```c#
IRemoteServer remoteServer = RemoteServerManager.FindServer(id);
```

## Server Groups
The `IServerGroup` interface represents a single server group and provides methods to access the remote server objects in that group. It also provides events that allow you to detect servers joining and leaving that group, in a similar way to the client manager does.

### Using Events
You can subscribe to join an leave events which are triggered when the registry informs the server about the new/leaving remote server.
```c#
serverGroup.ServerJoined += ServerJoined;
serverGroup.ServerLeft += ServerLeft;

void ServerJoined(object sender, ServerJoinedEventArgs args)
{
    // Do something here
}

void ServerLeft(object sender, ServerLeftEventArgs args)
{
    // Do something here
}
```

### Accessing Servers
To get all remote servers in a group the following code can be used.
```c#
IRemoteServer[] remoteServers = serverGroup.GetAllRemoteServers();
```

## Remote Servers
The `IRemoteServer` interface is the logical equivalent of the `IClient` interface for servers. It provides methods to interact with a remote server.

Note, that an instance of `IRemoteServer` is not always connected to the remote server in particular as connection is established following the server joining and if a reconnection is underway. Accessing certain properties on the `IRemoteServer` may return `null` and sending messages may return `false` if the remote server is not connected when the operation is performed.

### Sending messages
Sending messages is done in much the same way you would send to a client.
```c#
using (DarkRiftWriter writer = DarkRiftWriter.Create())
{
    writer.Write(someData);

    using (Message message = Message.Create(0, writer))
    {
        bool success = remoteServer.SendMessage(message, SendMode.Reliable);
    }
}
```

### Receiving Messages
Similarly receiving messages is fairly similar to receiving from a client.
```c#
remoteServer.MessageRecevied += MessageRecevied;

void MessageReceived(object sender, ServerMessageReceivedEventArgs e)
{
    using (Message message = e.GetMessage())
    using (DarkRiftReader reader = message.GetReader())
    {
        // Do something
    }
}
```
