# The Unity Scene
## Setting Up DarkRift
In a blank Unity scene add an empty GameObject, rename it `Network` and then add a `DarkRift -> Client` component.

This is the first fundamental DarkRift part, the client script connects to the server for you and allows you to send and receive messages - plus it even has some useful tools built in for you! Let’s look at the Unity interface for it.

![UnityClient settings](~/images/getting_started/unity_client.png "The settings for the UnityClient")

- `Address` is the host IP address that the server you wish to connect to is located on, by default it’s set to local host (the same computer you’re running the game on) for development.
- `Port` is the port number you’ve set the server to listen on, by default it’s 4296.
- `IP Version` is the type of IP address that you want to connect to the server with, this is most likely IPv4.
- `Auto Connect` means that the client will try and establish a connection when Unity invokes the Start method. If you turn this off, you’ll need to manually call UnityClient.Connect() from your code.
- `Invoke From Dispatcher` means the client will raise all events from Unity’s main thread so that you don’t need to worry about synchronization and multi-threading. If you disable this, you’ll need to handle synchronization and dispatching to the main thread yourself but you should see performance benefits. See Dispatcher.
- `Sniff Data` Data means that the client will print the details of messages received for debugging purposes.

Leave everything as is.

## Players
Download or make a white circle with a transparent background so we can colour it for different players, the higher the resolution the better. I used this [big white circle](https://goo.gl/images/hK8leu).

Create a C# script called `AgarObject` and add the following helper functions that we’ll come to use later:
```csharp
[RequireComponent(typeof(Renderer))]
public class AgarObject : MonoBehaviour
{
    [SerializeField]
    [Tooltip("The speed that the player will move.")]
    float speed = 1f;

    [SerializeField]
    [Tooltip("Multiplier for the scaling of the player.")]
    float scale = 1f;

    Vector3 movePosition;

    void Awake()
    {
        movePosition = transform.position;
    }

    void Update()
    {
        if (speed != 0f)
            transform.position = Vector3.MoveTowards(transform.position, movePosition, speed * Time.deltaTime);
    }

    public void SetColor(Color32 color)
    {
        Renderer renderer = GetComponent<Renderer>();
        renderer.material.color = color;
    }

    public void SetRadius(float radius)
    {
        transform.localScale = new Vector3(radius * scale, radius * scale, 1);
    }

    public void SetMovePosition(Vector3 newPosition)
    {
        movePosition = newPosition;
    }
}
```
Add this script to a new Sprite GameObject and assign the white circle sprite to the sprite field of the `SpriteRenderer`. Make it a prefab called `NetworkPlayer` and then create a new C# script called `MouseController`:
```csharp
[RequireComponent(typeof(AgarObject))]
public class MouseController : MonoBehaviour
{
    AgarObject agarObject;

    void Awake()
    {
        agarObject = GetComponent<AgarObject>();
    }

    void Update ()
    {
        Vector3 mousePoint = Camera.main.ScreenToWorldPoint(Input.mousePosition);
        mousePoint.z = 0;

        agarObject.SetMovePosition(mousePoint);
	}
}
```
Add the `MouseController` script to the `NetworkPlayer` GameObject and make it a separate prefab called `ControllablePlayer` (for clarity you should now have **2 versions**: `NetworkPlayer` **without** `MouseController` and `ControllablePlayer` **with** `MouseController`). You should be able to test your player and get a feel for the right speed value for you.

Make sure the **speed** on the `NetworkPlayer` is maybe **1.5x** the speed on the `ControllablePlayer`.

## Food
Download or make a food-like white square with a transparent background so we can give it different colours. Since it’s a square the transparent background might not be necessary. I used this [big white square](https://goo.gl/images/lOA64v).
        
Make a new prefab consisting only of the square and the `AgarObject` script, set the **speed** of it to **0** and then ensure your scene only consists of our `Network` object and `Main Camera`.

## Camera
Lastly, before we get started with the networking create a `CameraFollow` script containing the following:
```csharp
public class CameraFollow : MonoBehaviour
{
    [SerializeField]
    public float speed = 5f;

    public Transform Target { get; set; }

    void Update ()
    {
        if (Target != null)
        {
            Vector3 targetPos = Target.GetComponent<Renderer>().bounds.center;
            transform.position = Vector3.Lerp(
                transform.position,
                new Vector3(targetPos.x, targetPos.y, transform.position.z),
                speed * Time.deltaTime
            );
        }
    }
}
```
Add the `CameraFollow` script to the `Main Camera` so we can use it later and set the **camera’s background** colour to a **white** colour.

Because we’re making a 2D game make sure your **Scene View** is in **2D mode** and make the camera **orthographic**.

Now is a good time to save and backup your work/commit if you haven’t done so yet. That’s all the client side logic we’re going to do for now!
