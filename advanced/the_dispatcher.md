# The Dispatcher
%DarkRift is frequently used with software like Unity in both the client and server however Unity doesn’t support access through multiple threads and so usually forces code onto a single thread.

**Multithreading warning! Make sure you know exactly what you’re doing when adding concurrency to your project! If you don’t know what you’re doing, you need to learn the basics before continuing!**

To use it in single threaded contexts, %DarkRift has a dispatcher which, when either ThreadSafe (in plugins) is false or InvokeFromDisptacher (in the client) is true, it will invoke the events from. This allows %DarkRift to multiplex multiple threads onto a single, main thread to execute the events from but still allows %DarkRift to do the majority of its work in a multithreaded context.

Of course, this still isn’t ideal. If you are able to write multithreaded code then it is a shame to merge the threads so early because it means your plugins can’t take advantage of the parallelism. By leaving ThreadSafe as true or InvokeFromDispatcher as false %DarkRift will invoke things normally and then you can choose the point to merge threads using the dispatcher yourself.

The dispatcher is housed within the %DarkRift.dll assembly and the %DarkRift.Dispatching namespace; the server’s instance can be accessed through the Plugin.Dispatcher property and the client’s through the UnityClient.Dispatcher property. To use the dispatcher, you simply need to pass it the method you want to call and specify whether you want to wait for the task to be executed before resuming execution on the current thread etc., %DarkRift will then handle the rest!

## Using the Dispatcher With Unity
It's worth noting here that if you are creating your own dispatcher instance the dispatcher must be constructed on the main thread since it locks itself to that thread (to avoid you accidenlty executing tasks from 2 different threads). In Unity, MonoBehaviours are constructed from an instantiation thread rather than the main thread (and hence the constructor and all field initializers are executed from the instantiation thread); so if you create the dispatcher in a field initializer and try to execute tasks from Update (etc.) you will receive an error.

To avoid this, create the dispatcher in Awake or Start.
