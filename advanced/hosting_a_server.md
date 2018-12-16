# Hosting a Server
For some applications the basic console server might not fulfill all needs, for instance if you want to embed DarkRift into a Unity instance or perhaps run it from an cloud container.

Note: By ‘hosting’ a server here, we’re referring to running a server from your application or process, not hosting a server in the cloud!

To host a DarkRift server from your process is easy, you simply need to create a new DarkRiftServer object and pass it a ServerSpawnData object which contains the data you usually put in the Server.config file. Lastly you need to invoke DarkRiftServer.Start().

From there you would normally create a new thread to listen for console input and pass it to the server through DarkRiftServer.ExecuteCommand and have your main thread instruct the server to process all dispatcher items periodically.

The ServerSpawnData can be created in a few ways, normally it’s taken from the Server.config XML file using the ServerSpawnData.CreateFromXml factory method but in your applications, you can just create it as a normal object if you need.

If you’re trying to embed the server into a Unity scene then use the XmlUnityServer script provided and all of this will be done for you!
