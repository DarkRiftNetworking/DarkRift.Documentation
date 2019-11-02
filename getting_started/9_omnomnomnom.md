# Omnomnomnom
Let’s eat. I feel like our strange circles floating around their empty abyss are probably getting hungry by now…

For this I’m deliberately going to leave out simple code snippets since you should now have a good enough understanding to be able to add the easy bits yourself. If you’re struggling, go back and look through the previous chapters and the code you’ve already written and, if all else fails, look it up in the complete project.

Add a new C# file to our server plugin called `AgarFoodManager` and make the class a DarkRift plugin (first challenge!). Make sure you set `ThreadSafe` to **false** so you don’t have problems later!

Add a `HashSet` field called food that contains `FoodItem` objects and initialise it empty. Add the following definition of `FoodItem` to a new file:
```csharp
public class FoodItem : IEdible
{
    public ushort ID { get; set; }
    public float Radius => 0.1f;
    public float X { get; set; }
    public float Y { get; set; }

    public byte ColorR { get; set; }
    public byte ColorG { get; set; }
    public byte ColorB { get; set; }

    public FoodItem(ushort ID, float x, float y, byte colorR, byte colorG, byte colorB)
    {
        this.ID = ID;
        this.X = x;
        this.Y = y;
        this.ColorR = colorR;
        this.ColorG = colorG;
        this.ColorB = colorB;
    }
}
```
Now add the following definition of `IEdible` to a new file and make Player implement the `IEdible` interface (see where this is going?).
```csharp
public interface IEdible
{
    ushort ID { get; }
    float X { get; }
    float Y { get; }
    float Radius { get; }
}
```
In the food manager **constructor** add a number of **food** items (I did 20) with random values to the food `HashSet` similarly to how we assigned values to the players. For the **ID** field just assign them **incrementing** values from **0** so we can identify then uniquely later.

Now for a more challenging part! Make it so that when a player **connects** to the server the `AgarFoodManager` sends the **latest positions** of all **food** to the new client, ideally this should be in **one** message as we did before with the players (hint!) but if you’re struggling just implement it as 1 message per food item and get that working first. Create a dedicated food spawn **tag** for this.

Switching to the Unity project, add a `FoodManager` MonoBehaviour and attach it to the `Network` object. In this script, you’ll need to pass **in** the **client** and **receive** the **list of food** that the server sends, again you can probably reuse some of the player code here. Make sure you track the food items you add using a **dictionary** (ID as key) so that we can access them later.

You should be able to test now and see lots of food appear on the map with your players!

While we’re testing, add some code to set the `CameraFollow.Target` on our `Main Camera` to our player so that it follows them around.

Let’s switch back to `AgarPlayerManager` and add in some code to actually eat the items of food! In our `MovementMessageReceived` method add the following code:
```csharp
player.X = newX;
player.Y = newY;

AgarFoodManager foodManager = PluginManager.GetPluginByType<AgarFoodManager>();

foreach (FoodItem food in foodManager.Food)
{
    if (Math.Pow(player.X - food.X, 2) + Math.Pow(player.Y - food.Y, 2) < Math.Pow(player.Radius, 2))
    {
        player.Radius += food.Radius;
        SendRadiusUpdate(player);
        foodManager.Eat(food);
    }
}
```
It should be fairly easy to make out the **Pythagoras** in there. Essentially, we’re just checking the **distance** between the **food** and the **player** is less than the player’s **size** in a makeshift collision routine, we can then increase our player’s size appropriately and “eat” the food.

This won’t compile yet because there’s three missing items; before we add them, let’s look at the `GetPluginsByType` call. Since DarkRift handles the instantiation of plugins you can’t get a reference to other plugins without querying DarkRift because DarkRift is the entity with that reference so that’s exactly what we do here.

Hopefully you can see that the `Food` property and the `Eat` method need to be implemented in the `AgarFoodManager` class so add the following line:
```csharp
public IEnumerable<FoodItem> Food => food;
```
The `SendRadiusUpdate` function should **send** a message containing the **new radius** to every client using a **radius update tag**, you’ll also need to check the tag in `NetworkPlayerManager` on the client and either call `SetMovePosition` or `SetRadius` on the `AgarObject` so it does the right action!

Lastly, have a go at implementing the `Eat` method. All this needs to do is **move** the item of food to another, **random location** and **inform** all connected clients of this. Add yourself another **tag** value for issuing the move command.

The only thing that needs adding on the client now is some code to handle the movement of food items, everything else is already handled by the server side logic! Have a go at adding it yourself.

If you have trouble with the collisions not lining up with your graphics try adjusting the scale value on the AgarObjects.
