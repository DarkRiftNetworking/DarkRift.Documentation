# Unity Based Servers
Servers can be run using an Unity game as a host process to allow your server logic to take full advantage of many systems built into Unity such as Physics or NavMesh and many third party assets designed to work with Unity.

To run a server from within Unity add an XmlUnityServer component to any game object. The inspector then needs you to supply an XML configuration file and shows a few Unity-only settings. An example configuration is available in the DarkRift subdirectory with the server files.

Whilst it is possible to add your server logic using plugins, when in Unity it's recommended that you instead add your logic into the Unity scene as you would any other game and call down to the DarkRift server through the `XmlUnityServer.Server` property. This gives you additional privilleges that plugins do not have (such as the ability to stop the server) but at the cost of some features (such as commands).

It is still possible to add plugins however. Any DLLs containing plugins placed in the Unity project or scripts containing a Plugin will be detected and be visible in the XmlUnityServer's inspector automatically. They are then made available to the server and can be loaded via the configuration file.
