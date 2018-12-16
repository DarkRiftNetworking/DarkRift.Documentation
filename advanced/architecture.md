# Server Architecture
At the beginning of the tutorial you saw a diagram showing how DarkRift was very roughly composed, hopefully that gave you a good basic overview of how the server is structured, however, it’s a little more complicated than that for certain things to work.

Firstly, above the DarkRift server is a host process. DarkRift is no more than a library that can be embedded into things and so something must act as an overseer that can start it and watch it. For the purposes of this tutorial we’ve been using the Console Server which is essentially a small program that starts a server and pumps anything on the standard input stream as a command into DarkRift. Another popular choice of host process is a Unity project, doing this allows the server code to utilise Unity’s physics, navmesh and much more in order to simulate the players within game worlds.

Secondly, we mentioned the sniffer earlier but never touched on how it was implemented. Within DarkRift there are several internal plugins embedded that provide helpful functions like, development aids or overseers. Having these as plugins makes it much simpler to handle things like command input, could allow for more modular updates in the future and are generally a good idea. In fact, anything you can input commands into are implemented as plugins (although a couple of them have special privileges)!

Lastly, you probably saw right at the beginning that DarkRift said “Installed plugin AgarPlayerManager version 1.0.0” and completely dismissed it. We also briefly mentioned it earlier but DarkRift has a local data store in the Data folder (by default) which is a hidden folder if you’re looking for it. This contains several databases used internally, such as tracking installed plugins. Word of warning, don’t change the contents of these because it’ll be a nightmare to fix but it may be a good idea to include this folder in any automatic backup systems you have!

# Library Architecture
The individual components of DarkRift are fairly self-explanatory. All code common to both server and client is held in the DarkRift namespace, all code for the server in DarkRift.Server and all code for the client in DarkRift.Client.

Underneath the both client and server namespaces are DarkRift.Client.Unity and DarkRift.Server.Unity which holds some Unity specific code to make it easier to use (e.g. the monobehaviours that we attached to the game objects). To use DarkRift you don’t need the code in these and it would be perfectly possible to build a Unity game using the standard interface in DarkRift.Client/DarkRift.Server but it makes it integrate a little better!

Pro users will also find a DarkRift.Server.Helpers namespace (in DarkRift.Server.Helper.dll) that contains a number of helper classes that can make writing server side plugins easier! We’ll look more into this in a moment.
