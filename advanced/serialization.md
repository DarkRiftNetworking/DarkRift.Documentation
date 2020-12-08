# Advanced Serialization
In the tutorial we only explained the basics of manual serialization which will leave a significant amount of repeated code like:
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
It may be tempting to place this in a function to improve the maintainability, which wouldn't be wrong, but DarkRift already provides functionality for this. By adding making the object (in this case Player) implement the IDarkRiftSerializable interface, the serialization code can be easily associated to the object and DarkRift can also handle its serialization and deserialization for you.

```csharp
public Player()
{

}

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
```
When we ask DarkRift to serialise our Player object here, it will then pass a writer to our Serialize method which will dump our object’s contents. When we deserialize, we will ask DarkRift to read our object and DarkRift will create a new object using the blank constructor we just defined and will then call our Deserialize method which will extract the object from the reader. It is also possible to pass DarkRift an object to overwrite on deserialization to reduce garbage generation.

To serialize our IDarkRiftSerializable object we can now pass it straight into the message constructor and DarkRift will handle it for us:
```csharp
Player player = new Player();

//Set fields here

Message playerMessage = Message.Create(SPAWN_TAG, player);
```
It is also possible to serialize the IDarkRiftSerializable into a DarkRiftWriter to which more data can also be added around it:
```csharp
using(DarkRiftWriter writer = new DarkRiftWriter())
{
    //Add data here
    writer.Write(player);
    //Add more data here
}
```
This is doing similar but instead of loading the serializable straight into the message we’re loading it into a writer so that we can add multiple ones before putting it into the message. In the end this is all that DarkRift does internally in the first example anyway!

To deserialize the object we can pass DarkRift the type argument and it will generate the object for us:
```csharp
var reader = message.Deserialize<Player>();
```
Or if you are reading it from among other data:
```csharp
using(DarkRiftReader reader = message.GetReader())
{
    //Read data here
    Player player = reader.ReadSerializable<Player>();
    //Read more data here
}
```
