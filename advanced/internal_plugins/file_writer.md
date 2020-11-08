# File Writer
The `FileWriter` in the `DarkRift.Server.Plugins.LogWriters` namespace provides a log writer that writes to a file on disk.

## Settings
The following settings are exposed to configuration:

| Setting   | Default | Description |
|-----------|---------|-------------|
| `file` (required) | | The file to write to. |

The `file` setting can include parts of the current date/time using string format syntax where the first parameter is `DateTime.Now`:
```
Logs/{0:d-M-yyyy}/{0:HH-mm-ss tt}.txt
```
