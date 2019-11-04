# Disconnections
You’ve probably noticed by now that players don’t disappear when they exit the game, lets jump back into the code for a bit and resolve that.

## Disconnection Events
We can get the server to tell us when clients **disconnect** in the same way we get it to tell us when they **connect**. Add the following to the constructor of the `AgarPlayerManager` plugin:
```csharp
ClientManager.ClientDisconnected += ClientDisconnected;
```
I hope you can see the similarities! Now add a method that will clean up and inform **all** our remaining players:
```csharp
void ClientDisconnected(object sender, ClientDisconnectedEventArgs e)
{
    players.Remove(e.Client);

    using (DarkRiftWriter writer = DarkRiftWriter.Create())
    {
        writer.Write(e.Client.ID);

        using (Message message = Message.Create(Tags.DespawnPlayerTag, writer))
        {
            foreach (IClient client in ClientManager.GetAllClients())
                client.SendMessage(message, SendMode.Reliable);
        }
    }
}
```

## Removing on Unity
Once again this is nothing new, go ahead and try to implement it yourself. For this I **restructured** some of `PlayerSpawner`:
```csharp
void MessageReceived(object sender, MessageReceivedEventArgs e)
{
    using (Message message = e.GetMessage() as Message)
    {
        if (message.Tag == Tags.SpawnPlayerTag)
            SpawnPlayer(sender, e);
        else if (message.Tag == Tags.DespawnPlayerTag)
            DespawnPlayer(sender, e);
    }
}

void DespawnPlayer(object sender, MessageReceivedEventArgs e)
{
    using (Message message = e.GetMessage())
    using (DarkRiftReader reader = message.GetReader())
        networkPlayerManager.DestroyPlayer(reader.ReadUInt16());
}
```
And I **changed** the subscribe call to:
```csharp
client.MessageReceived += MessageReceived;
```
Finally, I added a `DestroyPlayer` method to our `NetworkPlayerManager`:
```csharp
public void DestroyPlayer(ushort id)
{
    AgarObject o = networkPlayers[id];

    Destroy(o.gameObject);

    networkPlayers.Remove(id);
}
```
You can also remove some of the checks from the `SpawnPlayer` routine.

Hopefully you’re now getting the hang of it and feel you can implement new features on your own. Once you know where things are in DarkRift it’s pretty much just the same principles replayed over!
