# Plugin System

We mentioned the sniffer in the tutorial but never touched on how it was implemented. Within %DarkRift there are several internal plugins embedded that provide helpful functions like, development aids or overseers. Having these as plugins makes it much simpler to handle things like command input. In fact, anything you can input commands into are implemented as plugins (although a couple of them have special privileges)!

You probably saw right at the beginning that %DarkRift said “Installed plugin AgarPlayerManager version 1.0.0” and completely dismissed it. We also briefly mentioned it earlier but %DarkRift has a local data store in the Data folder (by default) which is a hidden folder if you’re looking for it. This contains several databases used internally, such as tracking installed plugins. Word of warning, don’t change the contents of these because it’ll be a nightmare to fix but it may be a good idea to include this folder in any automatic backup systems you have!

Sections of this chapter:
- @ref md_advanced_plugin_system_managing_plugins
- @ref md_advanced_plugin_system_installs_and_upgrades
- @ref md_advanced_plugin_system_plugin_loading
- @ref md_advanced_plugin_system_commands
- @ref md_advanced_plugin_system_internal_plugins_index