# Plugin Loading
The following steps describe how DarkRift loads plugins. This can be useful to know when debugging errors loading plugins or their dependencies.

1. Types inheriting from `PluginBase` are discovered and registered with the plugin factory from the following sources:
    1. Any types specified directly in the configuration for the server. This includes any types the Unity integration finds in your project if you are using a Unity based server.
    2. Types loaded from the DLLs found in the plugin search paths specified in configuration. See below for more detail.
    3. The set list of types for internal plugins. These include the base commands, network listeners, etc.
2. Instances of plugins are loaded (instantiated) from the plugin factory based on the configuration. Plugins are loaded in the following order:
    1. Log writers
    2. Metric writers
    3. ServerRegistryConnectors
    4. Plugins (i.e. those implementing `Plugin`)
    5. Network listeners
3. The loaded even is run on plugins

## Plugin Search Paths
Plugin search path in configuration tell DarkRift where to find types that contain plugins.

Plugin search paths can point to a directory with one of more plugin files or a single DLL file. When a directory is specified, all subdirectories of that directory are also recursively searched.
```xml
<!-- A directory is specified -->
<pluginSearchPath src="LogWriters/" />
<!-- A file is specified -->
<pluginSearchPath src="NetworkListeners/MyListener.dll" />
```

When specifying a directory it is possible to instruct DR to create the directory if it does not exist.
```xml
<pluginSearchPath src="LogWriters/" createDir="true" />
```

### Plugin Dependency Resolution
When loading a DLL file, dependencies need to be resolved by DarkRift. By default, DarkRift will recursively search directories from the file that it is trying to load, however this behaviour can be altered.
```xml
<pluginSearchPath src="LogWriters/" dependencyResolutionStrategy="Standard" />
```

To clarify which libraries would be checked for dependencies for each of these values, consider the following file tree:
```
src
|- plugins
|  |- a
|  |  |- b
|  |  |  |- lib_1.dll
|  |  |
|  |  | plugin.dll
|  |
|  |- c
|  |  | lib_2.dll
|  |
|  |- lib_3.dll
|
|- DarkRiftServer.exe
|- lib_4.dll

```
And the following search path:
```xml
<pluginSearchPath src="plugins/" dependencyResolutionStrategy="..." />
```
The table below shows what which libraries would be checked when loading plugin.dll.

| DependencyResolutionStrategy | Description | Libraries checked in example when loading plugin.dll |
|------------------------------|-------------|--------------------------------------------------------|
| Standard | Uses the standard .NET assembly resolution with no enhancements. This strategy will search for dependencies for using the standard .NET rules detailed [here](https://docs.microsoft.com/en-us/dotnet/framework/deployment/how-the-runtime-locates-assemblies). | Any library previously loaded |
| RecursiveFromFile | Recursively searches all subdirectories of each file's containing folder in addition to the standard .NET assembly resolution. This strategy will search for dependencies for using the standard .NET rules detailed [here](https://docs.microsoft.com/en-us/dotnet/framework/deployment/how-the-runtime-locates-assemblies) but with additional steps to search through each discovered file's containing folder and any subdirectory of it. | `lib_1.dll`, any library previously loaded |
| RecursiveFromDirectory | Recursively searches all subdirectories of the folder's containing folder in addition to the standard .NET assembly resolution. This strategy will search for dependencies for using the standard .NET rules detailed [here](https://docs.microsoft.com/en-us/dotnet/framework/deployment/how-the-runtime-locates-assemblies) but with additional steps to search through the specified directory and any subdirectory of it. This cannot be used when using a path to a file. | `lib_1.dll`, `lib_2.dll`, `lib_3.dll`, any library previously loaded |

Some apparent inconsistencies may appear as plugins are loaded by DarkRift. For example, if a dependency is found for the first plugin loaded then it will be available to every subsequent plugin loaded even if the subsequent plugin's dependency resolution strategy would not discover it when loaded on its own.

## Loading Plugins
Once plugins have been loaded into the plugin factory, all types of plugins are loaded by their manager based on the configuration provided to the server. This configuration allows you to specify which of the loaded plugins are instantiated and allows you to pass settings to the plugins.

```xml
<logWriters>
  <!-- Specifying the log writer here marks it to be loaded -->
  <logWriter name="ConsoleWriter1" type="ConsoleWriter" levels="info, warning, error, fatal" />

  <!-- The log writer here is loaded with additional settings -->
  <logWriter name="FileWriter1" type="FileWriter" levels="trace, info, warning, error, fatal">
    <settings file="Logs/{0:d-M-yyyy}/{0:HH-mm-ss tt}.txt" />
  </logWriter>
</logWriters>
```

Additional settings can be passed to any type of plugin and become available in the constructor of the plugin:
```csharp
public FileWriter(LogWriterLoadData logWriterLoadData) : base(logWriterLoadData)
{
    // Get the log directory from settings or use a default
    LogFilePath = logWriterLoadData.Settings["file"] ?? "my/default_path/";
}
```

Configuration is very similar for all types of plugins. The only differences are: Plugins inheriting from `Plugin` are, by default, loaded by default and have an additional `load` property which which to disable them, all other types of plugins are only loaded when specified; and some types have additional arguments that must be specified such as log writers which must have `name` and `levels` properties.

<plugins>
  <!-- Health check is explicitly disabled -->
  <plugin type="HttpHealthCheck" load="false" />
</plugins>

