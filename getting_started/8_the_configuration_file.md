# The Configuration File
Let’s have another moment out to look through the configuration file. The configuration file holds lots of settings that DarkRift uses to start up including where to look for plugins, what to log and how to operate on the network.

Open the Server.config file that’s in the same directory as the server executable.

This file path can be specified as the first argument when you invoke the server from the command line, i.e. `DarkRift.Server.Console.exe "MyPath.config"`, but by default it will just search for `Server.config`. Do note, any settings changed in the server configuration won’t be reflected in DarkRift until you restart the server.

The `server` tag defines how DarkRift operates on the network, for example if you wanted to change port then you’d find that setting here. In general, this is settings for the properties found in the `ClientManager`.

The `pluginSearchPath` tag defines where DarkRift will load plugins from. You can specify individual files or entire folders to load and when specifying folders you can also specify whether DarkRift should create the folder if it's not present.

The `logging` tag instructs DarkRift how to log output to the various channels available to it. It should be fairly obvious what these do from their names so if you want to reroute certain log levels to different outputs then you can edit the rules here.

The `plugins` tag defines which plugins should be loaded into DarkRift, the loadByDefault attribute is currently instructing DarkRift to load all the plugins it finds but you can specify individual plugins to load or not load:
```xml
<plugins loadByDefault="false">
  <plugin type="AgarPlayerManager" load="true" />
</plugins>
```
This is very similar to the `logWriters` section as we are defining which plugins/logWriters to load in both cases. You can also specify a `settings` element with attributes which will be passed into the constructor of your plugin.

The `data` tag defines how DarkRift should store persistent data. DarkRift stores a number of databases so you can adjust settings about them here. The data directory is hidden by default in case you’re looking for it!

Lastly, the `databases` tag allows you to specify database connection strings for your plugins to retrieve later so they can connect to remote storage facilities.
