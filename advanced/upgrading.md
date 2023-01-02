# Upgrading from %DarkRift 1
Upgrading from %DarkRift 1 is relatively simple. The architecture of %DarkRift 1 and 2 are very similar and everything is still based around the modular plugin design. It's strongly reccomended to complete the tutorial before upgrading but this article with give you an overview of the major changes.

## Object Oriented Servers
The first major change is that %DarkRift 2 has moved to base servers off objects rather than providing a static interface for things like the ConnectionManager. This allows you run multiple servers in parallel much more easily than before and makes for a generally more object oriended design.

The isues this will cause with upgrades is that your plugins could now be loaded multiple times by different server objects. Whilst %DarkRift wont do this on its own, it may do so in future versions for scalability. Any static members in your plugins should ideally be avoided and appropriate care should be taken when accessing external resources (e.g. files).

The client side has also moved to an object oriented interface which may require you to pass new references to the client object if you were previously using DarkRiftAPI.

## Fully Authoritative Server
In %DarkRift 1 the client chose where it wanted the data to be sent to (e.g. to a specific ID, to all other clients, etc.) however this was poor design and encouraged people to give clients more control that they should've had. It also encouraged people to use passive servers that made no attempt to control the data passing through it, which is even worse.

Hence, %DarkRift 2 is fully authoritative and the server controls where the data will go rather than allowing the client an opinion. This makes for a much more secure and robust design and encourages developers to secure their servers and validate data.

Unfortunately, this will be one of the larger problems upgrading as there is no replacement for things like DarkRiftConnection.SendToAll and all data must be sent in a way akin to DarkRiftConnection.SendToServer. However, upgrading from this design certainly isn't a bad idea.

## Fully Manual Serialization
The other major problem upgrading is if you have been using %DarkRift 1's automatic serialization as %DarkRift 2 only has manual serialization now.

The automatic serialization was a huge bottleneck in %DarkRift (i.e. generally took longer than actually transmitting the data) and caused beginners a lot of problems with errors. Removing this provides a huge performance boost and encourages developers to control their object serialization more tightly.

If you have been using automatic serialization you will see in the tutorial how to use manual serialization and may also find the IDarkRiftSerializable interface reduces the effort to upgrade slightly.

## XML Configuration files
This is unlikely to cause too many problems upgrading but the bespoke, old and disgusting configuration files have been replaced with XML to make them more familiar, more robust and allow more intricate trees of settings to be built.

Because of this there is no direct equivalent for the ConfigReader class in %DarkRift 2 and you should instead use the extensive and very good XML tools provided in the .NET library.

## Licensing Changes
In %DarkRift 1 the only difference in licenses was that the Pro and Extreme versions had higher CCU (concurrent user) limits and the free version was available with development in mind.

In %DarkRift 2 the differences in licenses are based around features rather than CCU. The free version no longer has a CCU limit but instead is lacking a number of features that the Pro version has. The full comparison is available on the %DarkRift website.

It's also important to note that there are now only two licenses (Free and Pro) rather than the three there were for %DarkRift 1.

## Naming
In %DarkRift 2 a large number of the names have been changed to better reflect their purpose and use in %DarkRift 2. The following tables show the direct replacements for %DarkRift 1 classes and is intended as a lookup.

See the reference documentation for more information on each class.

### Namespaces
| Name in %DarkRift 1 | Equivalent in %DarkRift 2 |
|------------------------|------------------------------|
| %DarkRift (Common classes in Transmission.dll) | %DarkRift |
| %DarkRift (in %DarkRift.dll) | %DarkRift.Server |
| DarkRiftAPI | %DarkRift.Client |

### Common
| Name in %DarkRift 1 | Equivalent in %DarkRift 2 |
|------------------------|------------------------------|
| DarkRiftReader | DarkRiftReader |
| DarkRiftWriter | DarkRiftWriter |
| DistributionType | No equivalent |
| NetworkMessage | Message |

### Server
| Name in %DarkRift 1 | Equivalent in %DarkRift 2 |
|------------------------|------------------------------|
| ConfigTools.ConfigReader | No direct equivalent, use XML classes provided by .NET
| Storage.Database (and related) | No direct equivalent, see DatabaseManager and the database classes provided by .NET |
| Command | Command |
| ConnectionService | IClient |
| DarkRiftServer | DarkRiftServer |
| DarkRiftUtilities | Superseded with new messages |
| IManualDataProcessor | Superseeded by the Dispatcher |
| Interface | Superseded, to log a message use Plugin.Logger |
| Mode | Superseded by ServerSpawnData |
| PerformanceMonitor | No direct equivalent |
| Plugin | Plugin |
| PluginManager | PluginManager |

### Client
| Name in %DarkRift 1 | Equivalent in %DarkRift 2 |
|------------------------|------------------------------|
| DarkRiftAPI | No direct equivalent, use DarkRiftClient |
| DarkRiftConnection | DarkRiftClient |

## Unity
The main differences for Unity users is that the client and server are available as Unity components and so the details about them can be set in the inspector rather than by code. The classes for these are in the %DarkRift.Client.Unity and %DarkRift.Server.Unity namespaces.

It is still possible to use a code interface however, if you prefer.
