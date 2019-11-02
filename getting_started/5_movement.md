# Movement
## Sending Our Position
In the Unity project create a new C# script called `Player`, this is going to **send** our **position** to the server every time we **move**. Add the following code to it:
```csharp
using DarkRift.Client;
using DarkRift.Client.Unity;

public class Player : MonoBehaviour
{
    const byte MOVEMENT_TAG = 1;

    [SerializeField]
    [Tooltip("The distance we can move before we send a position update.")]
    float moveDistance = 0.05f;

    public UnityClient Client { get; set; }

    Vector3 lastPosition;

    void Awake()
    {
        lastPosition = transform.position;
    }

    void Update()
    {
        if (Vector3.Distance(lastPosition, transform.position) > moveDistance)
        {
            /* Send position to server here */

            lastPosition = transform.position;
        }
    }
}
```
Add it in your `ControllablePlayer` prefab. In our `PlayerSpawner` change our **if statement** in the **while loop** to:
```csharp
if (id == client.ID)
{
    obj = Instantiate(controllablePrefab, position, Quaternion.identity) as GameObject;

    Player player = obj.GetComponent<Player>();
    player.Client = client;
}
else
{
    obj = Instantiate(networkPrefab, position, Quaternion.identity) as GameObject;
} 
```
Now that’s done try to fill in the `Player` class with code to **send** our **position**. All that needs to be sent is the **x** and **y** components of transform.position.
If you’re stuck, here’s the code I wrote **after** the **distance if statement**:
```csharp
using (DarkRiftWriter writer = DarkRiftWriter.Create())
{
    writer.Write(transform.position.x);
    writer.Write(transform.position.y);

    using (Message message = Message.Create(Tags.MovePlayerTag, writer))
        Client.SendMessage(message, SendMode.Unreliable);
}
```
Don't forget tho add the tag to the `Tags` file:
```csharp
public static readonly ushort MovePlayerTag = 1;
```
None of this should look unfamiliar, we create a **writer** and package the data we want to send, put that inside a **message** with the **movement** tag and then send **unreliably** (if the server doesn’t get the position it’s not the end of the world because we’ll send another very shortly anyway).

## Updating the Server Position
On our server we need to track the positional changes of **each player** so that new players connecting always get the latest position, we also need to **send** this **movement** update message out to **all other players**.

In our **server** plugin append the following line to the ClientConnected method:
```csharp
e.Client.MessageReceived += MovementMessageReceived;
```
You’ll need to also add the **movement tag** constant to the `Tags` file (it was set to **1**).

Hopefully this line should look similar to what we did on the client, we’re telling the client object that whenever it receives a message it should invoke our handler to process it. Let’s add that handler now:
```csharp
void MovementMessageReceived(object sender, MessageReceivedEventArgs e)
{
    using (Message message = e.GetMessage() as Message)
    {
        if (message.Tag == Tags.MovePlayerTag)
        {
            using (DarkRiftReader reader = message.GetReader())
            {
                float newX = reader.ReadSingle();
                float newY = reader.ReadSingle();

                Player player = players[e.Client];

                player.X = newX;
                player.Y = newY;

                using (DarkRiftWriter writer = DarkRiftWriter.Create())
                {
                    writer.Write(player.ID);
                    writer.Write(player.X);
                    writer.Write(player.Y);
                    message.Serialize(writer);
                }

                foreach (IClient c in ClientManager.GetAllClients().Where(x => x != e.Client))
                    c.SendMessage(message, e.SendMode);
            }
        }
    }
}
```
Once again, the first few lines of this should look familiar. The **writer** here is just **updating** the contents of the message so we include the player **ID** (we need to know **who** sent it when we receive on the client) and we’re including the **radius** here for later. Lastly, we send off the message to **everyone** except the sender so they all update their position. Easy!
        
## Updating the Client Position</title>    
To **handle movement** on the game we’re going to move all clients with a single manager. Create a new C# script in your Unity project called `NetworkPlayerManager` and add the following:
```csharp
using DarkRift.Client;
using DarkRift.Client.Unity;

public class NetworkPlayerManager : MonoBehaviour
{
    [SerializeField]
    [Tooltip("The DarkRift client to communicate on.")]
    UnityClient client;

    Dictionary<ushort, AgarObject> networkPlayers = new Dictionary<ushort, AgarObject>();

    public void Add(ushort id, AgarObject player)
    {
        networkPlayers.Add(id, player);
    }
}
```
Add the script to our `Network` object and assign the client field. Add the following field to `PlayerSpawner` and assign it in the **inspector**:
```csharp
[SerializeField]
[Tooltip("The network player manager.")]
NetworkPlayerManager networkPlayerManager;
```
Finally add this line to the **bottom** of the **while loop** in your `SpawnPlayer` method:
```csharp
networkPlayerManager.Add(id, agarObj);
```
Have a go at writing the logic to receive the movement messages yourself, don’t forget you can **look back** at the receive code in `PlayerSpawner` and the plugin code we wrote for reference. If you get stuck, here’s my implementation:
```csharp
public void Awake()
{
    client.MessageReceived += MessageReceived;
}

void MessageReceived(object sender, MessageReceivedEventArgs e)
{
    using (Message message = e.GetMessage() as Message)
    {
        if (message.Tag == Tags.MovePlayerTag)
        {
            using (DarkRiftReader reader = message.GetReader())
            {
                ushort id = reader.ReadUInt16();
                Vector3 newPosition = new Vector3(reader.ReadSingle(), reader.ReadSingle(), 0);

                if (networkPlayers.ContainsKey(id)) 
                    networkPlayers[id].SetMovePosition(newPosition);
            }
        }
    }
}
```
Go into Edit -> Project Settings -> Player and **check** “Run in Background”.

Build it and test it! We should have movement!
