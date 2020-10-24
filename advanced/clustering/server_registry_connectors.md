# Writing Server Registry Connectors
Server registry connectors are a type of DarkRift plugin that provide DarkRift with a directory of the servers in a cluster and inform DarkRift of new servers joining or other servers leaving the cluster.

## Responsibilities
There are three main responsibility of a Server Registry:
1. Inform DarkRift when a new server joins the cluster
2. Inform DarkRift when a server leaves the cluster
3. Monitor for DarkRift servers that have unexpectedly left (optional, see later)

## `ServerRegistryConnector`
The `ServerRegistryConnector` is the base plugins type of all server registry connectors. There are two additional methods that require implementation, `RegisterServer` and `DeregisterServer` which are to add and remove the current server to/from the registry respectively.
```csharp
public class MyServerRegistryConnector : ServerRegistryConnector
{
    public override bool ThreadSafe => true;

    public override Version Version => new Version(1, 0, 0);

    protected override ushort RegisterServer(string group, IPAddress address, ushort port, NameValueCollection properties)
    {

    }

    protected override void DeregisterServer()
    {

    }
}
```

The `properties` parameter of `RegisterServer` is designed to allow DarkRift to pass additional information in future without needing to make breaking changes to the API.

When the connector detects that a server has joined or left the cluster it should call `HandleServerJoin(ushort id, string group, string host, ushort port, IDictionary<string, string> properties)` or `HandleServerLeave(ushort id)` respectively. These calls can be made for the current server and do not need to be filtered to the groups the current server needs to connect to, DarkRift will handle that logic for you.

## Detecting Unavailable Servers
Ideally a server registry would automatically watch the DarkRift server (e.g. via the HTTP health check) and automatically remove the server if it is unavailable. However, practically for some registries this is not possible therefore DarkRift provides a virtual method `HandleConnectionFailure` that a connector can override to be informed if DarkRift cannot connect to a server for some reason (including if the connection is lost and unable to be recovered).
