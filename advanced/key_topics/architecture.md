# %DarkRift Architecture

![Architecture Diagram](https://www.darkriftnetworking.com/DarkRift2/Docs/2.10.0/images/getting_started/architecture.png "DarkRift's Architecture")

At the beginning of the tutorial you saw the above diagram showing how %DarkRift was very roughly composed, hopefully that gave you a good basic overview of how the server is structured. This diagram and the archetypical server-client architecture explain the naming conventions used in %DarkRift:
- the server is an object that waits for connections from remote machines.
- the client is an object that initiates a connection to a remote machine.

You can set up other network architectures from these simple objects, even arbitrary peer-to-peer networks, by having more than one client and/or server objects in your game client process.

%DarkRift can run as a standalone host process, or be embedded in your game like any other C# assembly/class library.
- The Console Server is a small program running a %DarkRift server, which pumps anything on the standard input stream as a command into %DarkRift. Your code then runs as one or more plugins in this process.
- Another popular choice of host process is a Unity project, doing this allows the server code to utilise Unity’s physics, navmesh, and other engine specific features in order to simulate the players within game worlds.
- You can of course embed DarkRift directly into any .NET application.

See @ref md_advanced_hosting_index for a deeper discussion.

# Plugin Architecture

%DarkRift has a plugin system that either can be essentially ignored except during server configuration, or can be be used to separate your code into loosely coupled plugins by implementing DarkRift.Server.Plugin or one of its base classes. There are also a large number of internal plugins available in %DarkRift, see @ref md_advanced_plugin_system_internal_plugins_index.

Have a look at @ref md_advanced_plugin_system_index for a full treatment.

# Library Architecture
The individual components of %DarkRift are fairly self-explanatory. All code common to both server and client is held in the DarkRift namespace, all code for the server in DarkRift.Server and all code for the client in DarkRift.Client.

Underneath the both client and server namespaces are DarkRift.Client.Unity and DarkRift.Server.Unity which holds some Unity specific code to make it easier to use (e.g. the monobehaviours that we attached to the game objects). To use %DarkRift you don’t need the code in these and it would be perfectly possible to build a Unity game using the standard interface in DarkRift.Client/DarkRift.Server but it makes it integrate a little better!