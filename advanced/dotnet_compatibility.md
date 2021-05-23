# .NET Compatibility
DarkRift is build against multiple targets .NET versions for maximum compatibility. The following document is designed to help you choose a runtime or target version for plugins.

## Supported Runtimes
| DarkRift Build Type   | Built Against         | Supported Runtimes            |
|-----------------------|-----------------------|-------------------------------|
| .NET Framework        | .NET Framework 4.0    | .NET Framework 4.0 or above   |
| .NET Core             | .NET Core 2.0         | Any .NET Core 2.x runtime     |
| .NET Core 3.1         | .NET Core 3.1         | Any .NET Core 3.1 runtime     |
| .NET 5.0              | .NET 5.0              | Any .NET Core 5.0 runtime     |

## Supported Plugin Build Targets
| DarkRift Build Type   | Supported Build Targets for Plugins                                                                                               |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------|
| .NET Framework        | Any version less then the runtime in use (e.g. if run with .NET Framework 4.8.1, plugins may target up to .NET Framework 4.8.1)   |
| .NET Core             | Any .NET Core 2.x version less than the runtime in use (e.g. if run with .NET Core 2.1, plugins may target up to .NET Core 2.1)   |
| .NET Core 3.1         | .NET Core 3.1                                                                                                                     |
| .NET 5.0              | .NET 5.0                                                                                                                          |

## Library Build Targets
The following table documents the .NET versions used to build the DarkRift libraries. Note, library compatibility is low in order to support Unity versions 2017.4.22f or higher for backwards compatibility.

| DarkRift Build Type   | Built Against         |
|-----------------------|-----------------------|
| .NET Framework        | .NET Framework 3.5    |
| .NET Core             | .NET Standard 2.0     |
| .NET Core 3.1         | .NET Standard 2.0     |
| .NET 5.0              | .NET Standard 2.0     |
