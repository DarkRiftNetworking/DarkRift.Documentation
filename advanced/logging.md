# Logging
## Writing to Logs
Logging in DarkRift is primarily done via the `Logger` class. This class exposes a number of methods for logging at the different levels: Trace, Info, Warning, Error, and Fatal. The log type allows you to specify how important the event is and hence will also determine what files it is output to, dependent on the configuration file.

Each plugin is provided a default `Logger` available from the property `Plugin.Logger` but it is also possible to create your own `Logger` in Pro using `LogManager.GetLoggerFor()`.

## Configuring Log Output
There are 3 log writers built into DarkRift: the ConsoleWriter, the DebugWriter and the FileWriter. The ConsoleWriter will output any messages to `Console.WriteLine` and so will appear in the standard out stream or console window if it's not been redirected. The DebugWriter will output any messages to `Debug.WriteLine` and so will appear in Visual Studio's debug windows for example.

The FileWriter will output messages to the file that you specify allowing you to retain the logs easily. The file will be created at startup based on the path specified in the `file` attribute of the `settings` child element. E.g
```xml
<logWriter name="FileWriter1" type="FileWriter" levels="trace, info, warning, error, fatal">
  <settings file="Logs/{0:d-M-yyyy}/{0:HH-mm-ss tt}.txt" />
</logWriter>
```
The file path is formatted using `String.Format` where the first argument is DateTime.Now so files based on the date or time are possible (as shown above).

The ConsoleWriter also accepts a `useFastAnsiColoring` boolean setting which switches the way it colors text output to use ANSI control codes. This option does not work on some older Windows systems so you may wish to disable it to use the standard console coloring system. The default is true.

## Defining a Custom Log Writer
LogWriters are implemented in the same way as plugins are but instead of inheriting from Plugin they inherit from LogWriter and instead of taking PluginLoadData they take LogWriterLoadData. They're loaded in using the same code system as plugins so you just need to drop them into your Plugins directory for them to be loaded.

Each LogWriter has a WriteEvent argument that should handle writing the message to their output location. In most cases you should use WriteEventArgs.FormattedMessage as this is well optimized, will only be run once across all log writers and by keeping the same format for logs it is easier to read back.

Of course it's possible to access the individual components of the event such as the sender, message, the time that it was logged etc.
