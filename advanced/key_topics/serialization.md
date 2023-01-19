# Serialization

%DarkRift at its core is really just a way to pass messages (containing your game/application data) from one machine to another. To do that you need to be able to create (write) and interpret (read) such messages.

The most simple way to do serialization is to take a DarkRift.DarkRiftWriter and write some data. However, serialization of your objects can leave a significant amount of repeated code like:
```csharp
DarkRiftWriter newPlayerWriter = DarkRiftWriter.Create();
newPlayerWriter.Write(newPlayer.ID);
newPlayerWriter.Write(newPlayer.X);
newPlayerWriter.Write(newPlayer.Y);
newPlayerWriter.Write(newPlayer.Radius);
newPlayerWriter.Write(newPlayer.ColorR);
newPlayerWriter.Write(newPlayer.ColorG);
newPlayerWriter.Write(newPlayer.ColorB);
```
It may be tempting to place this in a function to improve the maintainability, which wouldn't be wrong, but %DarkRift already provides functionality for this. By enclosing the data to send in an object that implement the DarkRift.IDarkRiftSerializable interface, the serialization code can be easily associated to the object and %DarkRift can also handle its serialization and deserialization for you.

```csharp
class Player
{
    // Fields omitted

    // Default constructor is optional, but allows you to simplify code further
    public Player()
    {

    }

    // Deserialize will be called when the object is read from a reader or message
    public void Deserialize(DeserializeEvent e)
    {
        ID = e.Reader.ReadUInt32();
        X = e.Reader.ReadSingle();
        Y = e.Reader.ReadSingle();
        Radius = e.Reader.ReadSingle();
        ColorR = e.Reader.ReadByte();
        ColorG = e.Reader.ReadByte();
        ColorB = e.Reader.ReadByte();
    }

    // Serialize will be called when the object is written to a writer or message
    public void Serialize(SerializeEvent e)
    {
        e.Writer.Write(ID);
        e.Writer.Write(X);
        e.Writer.Write(Y);
        e.Writer.Write(Radius);
        e.Writer.Write(ColorR);
        e.Writer.Write(ColorG);
        e.Writer.Write(ColorB);
    }
}
```
When we ask %DarkRift to serialise our Player object here, it will then pass a writer to our `Serialize` method which will dump our object’s contents. When we deserialize, we will ask %DarkRift to read our object and %DarkRift will create a new object using the blank constructor we just defined and will then call our `Deserialize` method which will extract the object from the reader. It is also possible to pass %DarkRift an object to overwrite on deserialization to reduce garbage generation, in which case the blank constructor is not needed.

Note that the reader/writer objects used here are not wrapped in a `using` statement, %DarkRift handles the lifetime of the reader/writer for you.

To serialize our DarkRift.IDarkRiftSerializable object we can now pass it straight into the message constructor:
```csharp
Player player = new Player();

// Set fields here

Message playerMessage = Message.Create(SPAWN_TAG, player);
```
It is also possible to serialize the DarkRift.IDarkRiftSerializable into a DarkRift.DarkRiftWriter to which more data can also be added around it:
```csharp
using(DarkRiftWriter writer = new DarkRiftWriter())
{
    // Add data here
    writer.Write(player);
    // Add more data here
}
```
This does similar but instead of loading the serializable straight into the message we load it into a writer so that we can add additional data before adding the writer to the message.

To deserialize the object we do the converse:
```csharp
Player player = message.Deserialize<Player>();
```
Or if you are reading it from among other data:
```csharp
using(DarkRiftReader reader = message.GetReader())
{
    // Read data here
    Player player = reader.ReadSerializable<Player>();
    // Read more data here
}
```

To deserialize to an existing object you can pass a reference to it:
```csharp
message.DeserializeInto<Player>(ref player);
// Or
reader.ReadSerializable<Player>(ref player);
```
