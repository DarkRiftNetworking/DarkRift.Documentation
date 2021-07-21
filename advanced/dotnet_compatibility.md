# .NET Compatibility
DarkRift is build against multiple target .NET versions for maximum compatibility. The following document is designed to help you choose a runtime or target version for plugins.

## Supported Runtimes
The following table documents which versions of .NET DarkRift is compiled against and hence which .NET runtimes are able to run each version of DarkRift.

| DarkRift Build Type   | Built Against         | Supported Runtimes            |
|-----------------------|-----------------------|-------------------------------|
| .NET Framework        | .NET Framework 4.0    | .NET Framework 4.0 or above   |
| .NET Core             | .NET Core 2.0         | Any .NET Core 2.x runtime     |
| .NET Core 3.1         | .NET Core 3.1         | Any .NET Core 3.1 runtime     |
| .NET 5.0              | .NET 5.0              | Any .NET Core 5.0 runtime     |

## Supported Plugin Build Targets
The following table outlines which version of .NET plugins need to be compiled against in order to be loaded by each build of DarkRift.

| DarkRift Build Type   | Supported Build Targets for Plugins                                                                                               |
|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------|
| .NET Framework        | Any version less then the runtime in use (e.g. if run with .NET Framework 4.8.1, plugins may target up to .NET Framework 4.8.1)   |
| .NET Core             | Any .NET Core 2.x version less than the runtime in use (e.g. if run with .NET Core 2.1, plugins may target up to .NET Core 2.1)   |
| .NET Core 3.1         | .NET Core 3.1                                                                                                                     |
| .NET 5.0              | .NET 5.0                                                                                                                          |

## Library Build Targets
The following table documents which versions of .NET are used to build the DarkRift libraries. Note, library compatibility is low in order to support Unity versions 2017.4.22f or higher for backwards compatibility.

| DarkRift Build Type   | Built Against         |
|-----------------------|-----------------------|
| .NET Framework        | .NET Framework 3.5    |
| .NET Core             | .NET Standard 2.0     |
| .NET Core 3.1         | .NET Standard 2.0     |
| .NET 5.0              | .NET Standard 2.0     |

### Supported Unity Versions
The following table documents which versions of Unity support each DarkRift build. Note the values in this table are not dependent on the runtime DarkRift is compiled for but the version the DarkRift libraries are compiled against as the Unity integration does not use the DarkRift executables.

| DarkRift Build Type   | Required Scripting Runtime in Unity       | Minimum Unity Version     |
|-----------------------|-------------------------------------------|---------------------------|
| .NET Framework        | .NET 3.5 Equivalent                       | Unity 2017 or greater     |
| .NET Core             | .NET 4.x Equivalent or .NET Standard 2.0  | Unity 2018 or greater     |
| .NET Core 3.1         | .NET 4.x Equivalent or .NET Standard 2.0  | Unity 2018 or greater     |
| .NET 5.0              | .NET 4.x Equivalent or .NET Standard 2.0  | Unity 2018 or greater     |

DarkRift is currently shipped on the Unity Asset Store with a minimum version set to Unity 2018 as it is not possible to upload with any earlier versions. DarkRift however still supports 2017 and above technically.
