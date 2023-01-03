# Embedding a Server
For some applications the basic console server might not fulfill all needs, for instance if you want to embed %DarkRift into a Unity instance or perhaps run it from an cloud container.

To embed a %DarkRift server in your process is easy, you simply need to create a new `DarkRiftServer` object and pass it a `ServerSpawnData` object which contains the data you usually put in the `Server.config` file and `ClusterSpawnData` object to use server clustering in pro. Lastly you need to invoke `DarkRiftServer.Start()`.

From there you would normally create a new thread to listen for console input and pass it to the server through `DarkRiftServer.ExecuteCommand` and have your main thread instruct the server to process all dispatcher items by periodically calling the `DarkRiftServer.ExecuteDispatcherTasks` method.

The `ServerSpawnData` and `ClusterSpawnData` objects can be created in a few ways, normally they're taken from the `Server.config`/`Cluster.config` XML files using the `ServerSpawnData.CreateFromXml`/`ClusterSpawnData.CreateFromXml` factory methods, but if you need to build them from code the easiest way to do so is using a `DarkRiftServerConfigurationBuilder`/`DarkRiftClusterConfigurationBuilder` in `%DarkRift.Server.Configuration` which gives you a fluent API to construct them and handles a lot of the complexity for you.

If you’re trying to embed the server into a Unity scene then use the `XmlUnityServer` script provided and all of this will be done for you!
