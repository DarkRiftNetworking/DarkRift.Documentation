# Clustering
Clustering in DarkRift refers to running multiple DarkRift servers together in groups to horizontally scale and develop more modular architectures.

## Background
Traditionally, DarkRift has been primarily scaled vertically, by adding more CPU and memory resources to a single server, and optimization has largely been to make more efficient use of this resource. However, Most architectures require some form of horizontal scaling for both performance and resiliency.

It has always been possible to horizontally scale DarkRift servers with some manually work, most notably by using `DarkRiftClient` objects inside a plugin to connect to another 'upstream' server and usually forming a tree like architecture where servers can communicate to each other via their upstream. Similarly, segregating clients into different servers such that each server operates as a conceptual 'room' for clients is another common (and simpler) method of horizontal scaling.

DarkRift's clustering takes the first idea and builds on it to improve ease of use, but of course the latter idea is still achievable using DarkRift's clustering.

## Concepts
Clustering brings a few new concepts to DarkRift servers and the terminology explained here is important to understand.

When talking about a `server` we are referring to a single instance of a DarkRift application, i.e. a single process with plugins/logic. Two new concepts introduced with clustering are a `group`, which is considered to be a collection of at least one DarkRift server with the same plugins/logic (and purpose) as others in the group, and a `system` which is a collection of groups that are interconnected, communicate and operate together as an architecture.

When describing the system from within a server (e.g. in the .NET API) a `remote server` refers to another server in the system as a clarification. In this context, a 'server' refers to the server that code is currently being run on and 'remote server' refers to another server in the system. The same logic applies for a `remote group`.

A `ServerRegistryConnector` is a new type of plugin that handles the discovery of servers within a system. It is responsible for ensuring that a server knows of all other servers in its system so the necessary connections can be established between the servers. Normally a `ServerRegistryConnector` would be backed by a database or service discovery solution of some form (the `registry`) rather than doing the service discovery itself.

`Upstream` and `downstream` are two terms used to describe a connection between two groups or servers from the perspective of one of the groups/servers. An upstream connection means that this group/server is connecting to the remote group/server while a downstream connection means that the remote group/server is connecting to this group/server. It is always true that an upstream connection from the perspective of one server is considered a downstream connection from the perspective of the remote server.

An `external` server group is one in which `DarkRiftClient` objects can connect to while an `internal` server group is one other servers can connect to. A group cannot be both internally and externally visible.

## Architecture
DarkRift systems are composed of server groups which are composed of individual servers. When designing a DarkRift system you should primarily think about the different groups of servers; each group should have a well defined purpose and well defined connections to other groups.

Each server in a group should be identical so that the number of servers in each group can be scaled up or down without side effects on the system. Servers within a group cannot talk to other servers in the same group so if communication within a group is required it should be routed via another server group.

Connections between servers, while they appear without a direction in server logic, do in fact have a direction in reality as one group must establish the connection to another. This directionality has no effect on the functionality of the system but, since a group cannot be both externally and internally visibly at the same time, an external server will always establish connections to an internal server.

### Example
Consider a simple open world game where players can wander across a single large map shared by them all. A good architecture might be to establish three groups: **client servers**, **world servers**, and **social servers**.

**World servers** would handle all necessary logic for players in a small region of the map, **client servers** would be the externally visible entry point of the system for player to connect to and would route a player's communication to the **world server** responsible for the region of map the player is currently in, and social servers would allow players to send chat messages to other players that may be in a different region of the map (and hence **world server**) or connected to a different **client server**.

The communication lanes in this example are fairly simple: players connect to **client servers** which must be able to talk to all **world servers** to move players across the map and must be able to talk to all chat servers to ensure players can send messages to any other player.

![Architecture Diagram](~/images/advanced/clustering/example_architecture.png "Example Architecture")

It should be easy to think about the servers within these groups and how they interact. If we explode the diagram to contain 3 players, 2 client servers, 3 world servers and 1 social server you can see how they could interact.

![Exploded Architecture Diagram](~/images/advanced/clustering/example_architecture_exploded.png "Example Exploded Architecture")

If players 1 & 2 are in the same region of the map their communication would be routed to the same world server and they would be able to interact despite being on different client servers. If they were in different regions, and hence connected to different world servers, the players could still send messages to each other via the social server.

Obviously this is a very simple example and most applications will be more complex, it is only designed to give you an illustration of how groups should interact.
