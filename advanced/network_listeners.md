# Network Listeners
Network Listeners are specialized plugins that provide the lowest level of networking to DarkRift. They are responsible for maintining connections, passing the data over the network, and implementing the different send modes available in DarkRift.

The server is shipped with two network listeners and you've probably already used at least one. The first is the BichannelListener which sends reliable data via TCP and unreliable data via UDP. This is the default for the console based server. The second is a modification of the BichannelListener, the CompatibilityBichannelListener, that uses the same system but via a different .NET API to circumnavigate some bugs in hosts like Unity.

## Enabling a Network Listener</title>
NetworkListeners are configured in the listeners tag of the server configuration file. Each should have a unique name to identify them and a type which tells DR what Network Listener plugin to load. You can mix and match NetworkListeners to allow connections from multiple ports or via different protocols simultaneously etc.

An example of a listener element, that would create a BichannelListener, would be:
```xml
<listener name="DefaultListener" type="BichannelListener">
  <settings address="0.0.0.0"
            port="4296"
            ipVersion="IPv4"
            noDelay="true" />
</listener>
```
## Custom Network Listeners
It is possible to create your own NetworkListeners in a very similar way to creating plugins. Instead of inheriting from Plugin you must inherit from NetworkListener. When a new client connects you should create an instance of a NetworkServerConnection which DarkRift will use to communicate with that client.

**Note that it is assumed your NetworkListeners will be thread safe!**
```csharp
class MyNetworkListener : NetworkListener
{
    public override Version Version => new Version(1, 0, 0);

    public MyNetworkListener(NetworkListenerLoadData pluginLoadData) : base(pluginLoadData)
    {
    }
    
    public override void StartListening()
    {
      //When a new client connects we need to call RegisterConnection(NetworkServerConnection connection)
    }
}
```
```csharp
class MyNetworkServerConnection : NetworkServerConnection
{
        //Whether we're connected
        public ConnectionState ConnectionState { get; }

        //A list of endpoints we're connected to on the client
        public IEnumerable<IPEndPoint> RemoteEndPoints { get; }
        
        //Given a named endpoint this should return that
        public IPEndPoint GetRemoteEndPoint(string name)
        {
            if (name == "tcp")
                return tcpEndPoint;
            else
                throw new ArgumentException("Not a valid endpoint name!");
        }
        
        //Called once for initialization
        public void StartListening()
        {
        
        }
        
        //Sends a message reliably...
        public bool SendMessageReliable(MessageBuffer message)
        {
        
        }
                
        //...Sends a message unreliably!
        public bool SendMessageUnreliable(MessageBuffer message)
        {
        
        }
        
        //Called when the server wants to disconnect the client
        public bool Disconnect()
        {
        
        }
        
        //We should call HandleMessageReceived(MessageBuffer message, SendMode sendMode) when we get a new message from the client
        //And HandleDisconnection(...) if the client disconnects
}
```
Naturally, an appropriate connector is also required on the client side. This is done by inheriting from NetworkClientConnection and passing an instance to DarkRiftClient.Connect while connecting. This is very similar to implementing NetworkServerConnection:
```csharp
class MyNetworkClientConnection : NetworkClientConnection
{
    //Whether we're connected
    public ConnectionState ConnectionState { get; }

    //A list of endpoints we're connected to on the server
    public IEnumerable<IPEndPoint> RemoteEndPoints { get; }
    
    //Given a named endpoint this should return that
    public IPEndPoint GetRemoteEndPoint(string name)
    {
        if (name == "tcp")
            return tcpEndPoint;
        else
            throw new ArgumentException("Not a valid endpoint name!");
    }
    
    //Called when DarkRiftClient.Connect is called
    public void Connect()
    {
    
    }

    //Sends a message reliably...
    public bool SendMessageReliable(MessageBuffer message)
    {
    
    }
                
    //...Sends a message unreliably!
    public bool SendMessageUnreliable(MessageBuffer message)
    {
    
    }
    
    //Called when the server wants to disconnect the client
    public bool Disconnect()
    {
        
    }
    
    //We should call HandleMessageReceived(MessageBuffer message, SendMode sendMode) when we get a new message from the client
    //And HandleDisconnection(...) if the client disconnects
}
```
