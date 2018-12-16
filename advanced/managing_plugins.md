# Managing Plugins
There are 2 systems in DarkRift that oversee the plugins. The first is the PluginController which provides the interface commands for managing the plugins and the second is the PluginManager which handles the loading of plugins and generally performs lower level tasks. Whilst they have similar names they have very different purposes!

# Plugin Controller      
You may recall that we discussed how anything that takes command input is implemented internally in DarkRift as a plugin, a prime example of this is the PluginController.

Using the PluginController plugin you can uninstall any plugins previously loaded using `plugins uninstall <plugin-name>` and this will remove any information stored about them and any data they may have saved locally on the server.

You can also view the plugins installed and loaded (i.e. running) using `plugins installed`.

Adding the -h flag will also display the internal plugins that are usually hidden.

# Plugin Manager
The PluginManager is a very different beast, however. When DarkRift starts up, the PluginManager is responsible for loading and keeping track of all plugins and hence invokes the install/upgrade process and keeps track of what version each plugin is expected to be.

We can access other plugins that are loaded in the server via the PluginManager using GetPluginByType, GetPluginByName and GetAllPlugins plus an indexer that acts similarly to GetPluginByName. There are also methods for getting the meta information about a plugin such as GetInstalledVersion. These can all be used to get information about other plugins and communicate between them as needed.
