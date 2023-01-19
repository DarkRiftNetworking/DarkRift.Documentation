# Console Writer
The `ConsoleWriter` in the `%DarkRift.Server.Plugins.LogWriters` namespace provides a log writer that writes to the console.

## Settings
The following settings are exposed to configuration:

| Setting   | Default | Description |
|-----------|---------|-------------|
| `useFastAnsiColoring` | `true` | Whether to enable fast colouring of output using ANSI control codes. |

Not all consoles (particularly on Windows) support ANSI control codes so the `useFastAnsiColoring` setting allows for compatibility with those consoles.
