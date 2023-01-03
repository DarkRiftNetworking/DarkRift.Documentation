# The Strike System
Since there are a few things %DarkRift can detect that might indicate a hacked or malicious client (e.g. malformed packets, corrupt headers), %DarkRift has a built-in system for tracking warnings that Pro users have access to. This guide will show you some of the ways you can inteface with it.

## Adding Strikes
Firstly, if the client performs an action or does something you would deem as cheating you can add a strike to their record in %DarkRift. If the client accumulates too many strikes then %DarkRift will automatically kick them from the server and inform you.

To do this you simple call Strike on the client and provide some details for logs. A common example might be that the message they sent was too long or short:
```csharp
if (reader.Length != 8)
    client.Strike("Received movement message of invalid length.");
```
By centralising all anti-cheating/protection code into the strike system %DarkRift can also contribute strikes when it detects problems and will ensure it’s much harder to manipulate the system.

## Reacting to Strikes
When %DarkRift or your plugins strike a client the StrikeOccured event will be raised on the Client object so that you can react, forgive (i.e. discount the strike from the record) or perform any other action you wish.

To configure the number of strikes the client can have before they're kicked you can adjust the maxStrikes attribute of the server element in the configuration file.
