# Getting Started
To get started with server clustering you will need to setup a few things. This page will take you through the main steps to setting up your cluster.

## `ServerRegistryConnector`
The first step is to choose an appropriate `ServerRegistryConnector` for your cluster. This will be responsible for discovering servers within the cluster and passing that information to DarkRift.

At the time of writing the only official connector is for [Hashicorp Consul](https://www.consul.io/), and it can be downloaded [here](https://github.com/DarkRiftNetworking/consul-server-registry-connector). Other official connectors and also third party connectors may be available and may be more appropriate for your deployment.

It's worth noting that the connector is completely isolated from any code you need to write for clustering, therefore the choice of connector can be easily changed if necessary.

The connector is like any other plugin and must be placed in one of the plugin search paths. To enable the connector you must explicitly define it in your server configuration, for example:
```xml
<serverRegistry advertisedHost="...">
  <serverRegistryConnector type="ConsulServerRegistryConnector">
    <settings consulAddress="http://localhost:8500" />
  </serverRegistryConnector>
</serverRegistry>
```

## `Cluster.config`
In order for DarkRift to establish the correct network of connections you need to specify a `Cluster.config` file. This file is similar to the `Server.config` file but instead of describing the operation of a single server this file defines the groups in the cluster and how they are related to each other.

Before writing this file it is important to have thoroughly considered your architecture. While it is easy to change the architecture in DarkRift it may not be so simple to change your own logic!

A simple `Cluster.config` file might look like this:
```xml
<?xml version="1.0" encoding="utf-8" ?>

<cluster>
  <groups>
    <group name="Group1" visibility="external">
      <connectsTo name="Group2" />
    </group>

    <group name="Group2" visibility="internal" />
  </groups>
</cluster>
```

Each group is specified with a name and a visibility. The name is what will be used to reference this group in both server logic and by the registry connector. The visibility defines whether this server expects `DarkRiftClient` objects to connect to it (`external`) or other servers to connect to it (`internal`).

Each group can contain any number of `connectsTo` elements which specify that the servers in a group should establish connections to all other servers in another group. In the above configuration servers in `Group1` will automatically establish connections to servers in `Group2`.

A `connectsTo` element indicates the group is the downstream group in the connection and the group specified as being connected to is the upstream.

### Example
As an example, the following architecture would be defined by this `Cluster.config`:

![Architecture Diagram](~/images/advanced/clustering/example_architecture.png "Example Architecture")

```xml
<?xml version="1.0" encoding="utf-8" ?>

<cluster>
  <groups>
    <group name="ClientServers" visibility="external">
      <connectsTo name="SocialServers" />
      <connectsTo name="WorldServers" />
    </group>

    <group name="SocialServers" visibility="internal" />
    <group name="WorldServers" visibility="internal" />
  </groups>
</cluster>
```

## Server Group and Advertised Connection Details
Each server in a cluster needs to know what group it belongs to in order to establish the correct connections to other groups. Similarly, every server needs to have an advertised address and host with which it can be connected to by other servers, these details are stored in the registry.

To specify this the following properties need to be added to the `server` element of the `Server.config` file:
```xml
<server ... serverGroup="Group1"/>
```
And the following to the `serverRegistry` element:
```xml
<serverRegistry advertisedHost="hostname" advertisedPort="4296">
  ...
```

The `severGroup` must be the name of a group specified in the `Cluster.config` file.

The `advertisedHost` can be an IP address or a DNS name.

The `advertisedPort` value does not need to be the same as a port specified for a network listener to operate on, this defaults to 4298.

