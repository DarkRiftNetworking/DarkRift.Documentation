 # First Network Code
 ## Spawning Players
The first thing that we need to do is make players appear when someone logs in. We need to make sure that the person logging in receives a list of all players that are currently **online** and we need to make sure everyone else spawns our new player on their game.

Let’s take a step back though and plan what we need to store about a **player**:
- Their X, Y **position** in the world.
- Their **radius** (when they eat someone they’re going to get better).
- Their **colour**.
- Plus, in the future we might want to store a **name**.-

Go ahead and make a new `Player` class on the **server** that stores those. If you’re lazy like me, copy and paste this code in:
```csharp
class Player
{
    public ushort ID { get; set; }
    public float X { get; set; }
    public float Y { get; set; }
    public float Radius { get; set; }
    public byte ColorR { get; set; }
    public byte ColorG { get; set; }
    public byte ColorB { get; set; }
    
    public Player(ushort ID, float x, float y, float radius, byte colorR, byte colorG, byte colorB)
    {
        this.ID = ID;
        this.X = x;
        this.Y = y;
        this.Radius = radius;
        this.ColorR = colorR;
        this.ColorG = colorG;
        this.ColorB = colorB;
    }
}
```
I use **bytes** for the color because it means we have to store and send a lot **less data** than when using floats and it will serve the exact same purpose. Taking little steps like this can really improve bandwidth when you’re sending a message many times.

To **spawn** players we need to tell %DarkRift to inform us when a player connects. In the constructor of `AgarPlayerManager` add the line:
```csharp
ClientManager.ClientConnected += ClientConnected;
```
And add a new method:
```csharp
void ClientConnected(object sender, ClientConnectedEventArgs e)
{

}
```
The `ClientManager` is responsible for keeping track of any clients that are connected to the **server** so you can use it to access clients and it can inform you when clients **connect** or **disconnect**. In this line, we’re **subscribing** to the `ClientConnected` event so whenever someone connects our method will be called with the `ClientManager` in sender and details of the connection in the `ClientConnectedArgs`.

Let’s give our newly connected player their own `Player` object. In our `ClientConnected` method add code to generate them a **new player** setup with random values:
```csharp
Random r = new Random();
Player newPlayer = new Player(
    e.Client.ID,
    (float)r.NextDouble() * MAP_WIDTH - MAP_WIDTH / 2,
    (float)r.NextDouble() * MAP_WIDTH - MAP_WIDTH / 2,
    1f,
    (byte)r.Next(0, 200),
    (byte)r.Next(0, 200),
    (byte)r.Next(0, 200)
);
```
And at the top of the class add a `MAP_WIDTH` constant:
```csharp
const float MAP_WIDTH = 20;
```
You might notice the sneaky `e.Client.ID` hiding in there. So that we can address the player when it’s on the Unity side we need to have an **identifier**, since the client will only have one `Player` object they control it’s sufficient to just use the server's ID that gets allocated to the client by %DarkRift when it connects.

I’m using a value between 0 and 200 for each **colour** channel because if it’s too close to 255, 255, 255 we won’t be able to see the client against the **background**!

Next let’s send our new `Player` object to **all** the other **clients** so that they can spawn a new player in their games. Add the following just after:
```csharp
using (DarkRiftWriter newPlayerWriter = DarkRiftWriter.Create())
{
    newPlayerWriter.Write(newPlayer.ID);
    newPlayerWriter.Write(newPlayer.X);
    newPlayerWriter.Write(newPlayer.Y);
    newPlayerWriter.Write(newPlayer.Radius);
    newPlayerWriter.Write(newPlayer.ColorR);
    newPlayerWriter.Write(newPlayer.ColorG);
    newPlayerWriter.Write(newPlayer.ColorB);

    using (Message newPlayerMessage = Message.Create(0, newPlayerWriter))
    {
        foreach (IClient client in ClientManager.GetAllClients().Where(x => x != e.Client))
            client.SendMessage(newPlayerMessage, SendMode.Reliable);
    }
}
```
Whoa there! That got complicated quickly didn’t it!

Let’s look through each part. We need to **convert** our `Player` to something we can send over the network and currently it’s an unsigned short, 3 floats and 3 bytes, which isn’t compatible with the internet. The `DarkRiftWriter` and `DarkRiftReader` are objects provided for you to make that conversion easier, we **write** all our fields into the **writer** and on the Unity side we’ll **read** them off in the **same order** with a **reader**.

Next, we construct a `Message`. **Messages** essentially **wrap** data (all those values we stuffed into the `DarkRiftWriter`) and give them some simple header values that identify what the data actually **contains**. The first argument, **the tag**, is a single ushort that **identifies** what the message is about (think move player, use inventory object, send chat message etc.) and the second is our **data**. For now we can just use 0 as our tag.

Finally, we get **all clients** currently **connected** to the **server** using `ClientManager.GetAllClients()`, utilise a little LINQ to remove the client that just connected (we’ll deal with him later) and then **send** the message to **each** client. **Don’t forgot that when you call `GetAllClients()` the client that just connected has already been added!**

See? Easy! What were you worried about?

In all seriousness though, that’s pretty much 80% of everything there is to sending messages in %DarkRift, both client and server: create a **writer** for the **data**, a **message** to **wrap** the data and then **send**.

One thing I did gloss over, however, was the `SendMode`. This identifies how we should send the message to the client:
- `Unreliable` – **No guarantees** will be made that the other device gets the message but it’s a lot **faster** to send. Use this for **frequent** data that will soon be out of date anyway (e.g. position/rotation updates).
- `Reliable` – The message is **guaranteed** to be delivered and large messages will be split up into smaller messages so that they can be sent.

The next thing we need to do quickly is to spawn all other players on our newly connected player; since we want to minimise the bandwidth we use here we’re going to package it all into a single message and send it. Add the following line to the top of your `AgarPlayerManager`:
```csharp
Dictionary<IClient, Player> players = new Dictionary<IClient, Player>();
```
And then add this to the end of your `ClientConnected` method:
```csharp
players.Add(e.Client, newPlayer);

using (DarkRiftWriter playerWriter = DarkRiftWriter.Create())
{
    foreach (Player player in players.Values)
    {
        playerWriter.Write(player.ID);
        playerWriter.Write(player.X);
        playerWriter.Write(player.Y);
        playerWriter.Write(player.Radius);
        playerWriter.Write(player.ColorR);
        playerWriter.Write(player.ColorG);
        playerWriter.Write(player.ColorB);
    }

    using (Message playerMessage = Message.Create(0, playerWriter))
        e.Client.SendMessage(playerMessage, SendMode.Reliable);
}
```
Hopefully this should be fairly self-explanatory from what we’ve already done, the only difference is that we keep writing into the **same writer** so that it’s all in **one message**. Note that we add the new player to the dictionary before enumerating over it so that it’s included in what we send to the player (otherwise they wouldn’t spawn themselves a player!)

Another thing we skipped over was the `using` statement. Messages, DarkRiftWriters and DarkRiftReaders all implement `IDisposable`, not because they contain resources that need disposing of, but because when we call `Dispose` they are returned to an object pool so that next time you call Create they don't have to be reallocated. Technically the using statements are completely optional but you'll get much better performance with them left in! We'll cover recycling in more depth in the Advanced section.

Finally, it’s a good idea to define **constants** for **tags** so that you can easily refer to them and modify them without side effects. Add a new file called `Tags` and put in a **static** class to collect all our tags:
```csharp        
static class Tags
{
    public static readonly ushort SpawnPlayerTag = 0;
}
```
And change the tag parameter in both of the `Message.Create` calls to: `Tags.SpawnPlayerTag`.
        
## Actually Spawning Players
Now that we’ve written the server code for spawning players let’s add the **client** side code. Create a new file called `PlayerSpawner` in the Unity project and add the following code:
```csharp
using DarkRift.Client.Unity;

public class PlayerSpawner : MonoBehaviour
{
    const byte SPAWN_TAG = 0;

    [SerializeField]
    [Tooltip("The %DarkRift client to communicate on.")]
    UnityClient client;

    [SerializeField]
    [Tooltip("The controllable player prefab.")]
    GameObject controllablePrefab;

    [SerializeField]
    [Tooltip("The network controllable player prefab.")]
    GameObject networkPrefab;

    void Awake()
    {
        if (client == null)
        {
            Debug.LogError("Client unassigned in PlayerSpawner.");
            Application.Quit();
        }

        if (controllablePrefab == null)
        {
            Debug.LogError("Controllable Prefab unassigned in PlayerSpawner.");
            Application.Quit();
        }

        if (networkPrefab == null)
        {
            Debug.LogError("Network Prefab unassigned in PlayerSpawner.");
            Application.Quit();
        }

        client.MessageReceived += SpawnPlayer;
    }
}
```

In this you’ll notice we define a **reference** to a `UnityClient` object that we’ll fill from the **inspector**. This is the **component** that handles our **connection** to the server, we added it to the `Network` object in our Unity scene earlier, remember?

The `MessageReceived` event we **subscribe** to is called whenever the client **receives** a message from the server. You can subscribe as many different handlers to this as you like but having **fewer** is generally easier to maintain and a little **faster**.

One other important thing to note is that we call this from `Awake`. When you have the `UnityClient` set to connect in the Start routine it is important to make sure that you subscribe for all initial messages from `Awake` because otherwise you might miss messages if it connects before you subscribe!

Add the following code to **decode** our spawn packets:
```csharp
void SpawnPlayer(object sender, MessageReceivedEventArgs e)
{
    using (Message message = e.GetMessage())
    using (DarkRiftReader reader = message.GetReader())
    {
        if (message.Tag == Tags.SpawnPlayerTag)
        {
            if (reader.Length % 17 != 0)
            {
                Debug.LogWarning("Received malformed spawn packet.");
                return;
            }

            while (reader.Position < reader.Length)
            {
                ushort id = reader.ReadUInt16();
                Vector3 position = new Vector3(reader.ReadSingle(), reader.ReadSingle());
                float radius = reader.ReadSingle();
                Color32 color = new Color32(
                    reader.ReadByte(), 
                    reader.ReadByte(), 
                    reader.ReadByte(),
                    255
                );
    
                GameObject obj;
                if (id == client.ID)
                {
                    obj = Instantiate(controllablePrefab, position, Quaternion.identity) as GameObject;
                }
                else
                {
                    obj = Instantiate(networkPrefab, position, Quaternion.identity) as GameObject;
                }

                AgarObject agarObj = obj.GetComponent<AgarObject>();

                agarObj.SetRadius(radius);
                agarObj.SetColor(color);
            }
        }
    }
}
```
If you take a careful look into the code you’ll see that we just **reverse** the packaging process we did before: we get a **reader** from the **message** that contains the message data and simply loop through reading the data out in the same order we wrote it on (note, **order matters** here!) until there is no more data left. As we read through we create the necessary objects depending on whether the **ID** of the player object is our ID (if it is then that should be the object we’re **controlling**).

Add the `PlayerSpawner` component to the `Network` object in the scene and drag onto it the `Client` and **both prefabs** in their respective places.

Lastly, **copy** the `Tags` file from the **plugin** so we have an identical copy in our Unity project.

You should now be able to test and see a player spawn on the client. Next step, movement!
