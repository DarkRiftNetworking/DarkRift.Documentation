# Understanding DarkRift's Object Pools
This section will explain how DarkRift's object pools work at a conceptual level to recycle and reuse objects and avoid allocations.

## Why Recycle Objects
DarkRift pools and recycles objects for two reasons:
1. Reusing objects means that less memory is released to the garbage collector and thus less time is spent in garbage collection. While this is rarely an issue for most applications and decreasingly an issue with modern garbage collection algorithms it can still be an issue on some platforms like Unity where the GC will 'stop the world' and cause a brief intermission in program execution.
2. Avoiding allocations of new objects benefits performance substantially when doing work across multiple threads as there is no lock contention within the allocation algorithms. Since DarkRift uses threads from the thread pool, it can be significantly impacted by lock contention when allocating memory on the heap so by reusing objects from lock-less object pools wherever possible the contention is minimised.

There are a number of pre-existing libraries available for object pooling however a custom implementation is used within DarkRift as most of the existing libraries only address the first issue and continue to use a thread-shared store of objects.

## Object Cache vs Collection Cache
DarkRift has two sets of object pools: The Object Cache and the Collection Cache.

The object cache is a set of object pools for simple, self-contained, mutable objects such as `DarkRiftReader` or `MessageReceivedEventArgs` objects. The collection cache is a set of object pools for objects with a backing storage that may vary in size such as a `List`, `Dictionary`, or any array type.

The important distinction between them to note is whether there is a bounded store within the object. For example, a `List` object relies on a backing array which it can increase in size (by allocating a larger backing array) as more elements are added. By storing `List` objects in the collection cache, DarkRift can provide an instance of the `List` with a backing array of an appropriate size already, reducing the likelyhood that the `List` will need to expand and hence an allocation will occur.

## Balancing Across Threads
Both object caches and collection caches are implemented as 'thread local' pools with a 'balancing' pool across threads.

When an object is requested from a cache the cache first checks the thread static local pool to see if there are any available instances stored for that thread's sole use. If an object is found in the thread local pool then is it immediately returned. If the thread local pool is empty then the cache checks the balancing pool to see if there are any instances available.

If there are instances available in the thread balancing pool, then a 'rebalance' occurs and a number of instances (governed by the rebalance ratio setting) are copied from the balancing pool into the thread local pool for sole use by that thread. An instance is then returned. If there are no instances available in the thread balancing pool, then `null` is returned and the caller is expected to perform an allocation.

On returning an object a similar process occurs however a rebalance is triggered if the thread local pool is already full and instances are rebalanced up to the balancing pool to make room for the returned object. If the balancing pool cannot accept any new objects then it the returned object is left for the caller to dispose correctly.

This architecture means that there is very fast, thread independent and lock-less access to resources in the thread local pool (which is the majority of cases) but also cross-thread resource sharing via the balancing pool when one thread is producing more than it is consuming (or vice versa).
