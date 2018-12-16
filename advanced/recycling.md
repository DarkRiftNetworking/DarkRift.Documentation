# Recycling
DarkRift recycles a number of commonly used objects to relieve the pressure on the garbage collector and to speed up instantiate of the objects. This page will tell you more about the objects and customization available.

## Overview
DarkRift recycles 4 objects currently:
- DarkRiftWriters
- DarkRiftReaders
- Messages
- SocketAsyncEventArgs (used by DarkRift for all socket operations)

The instances of these objects are all managed by the ObjectCaches which are unique and static for each thread allowing very fast access to them. This does mean you can starve a thread of resources by retrieving instances from one cache and returning them to another.

To simplify recycling outside of DarkRift, the writers, readers and messages are IDisposable so that they can be used within using blocks to automatically dispose of them once they leave its scope. This has the added benefit that you cannot reference an recyclable object once it has been disposed since that would give you non-deterministic behaviour. Once Dispose has been called on the object you should not make reference to it again as it will be subject to change contents unexpectedly.

In future the following types will also be recycled:
- All byte arrays used by DarkRift
- All EventArg objects passed on event invocation

## Customization
The object cache can be customized at start up on both the client and server to configure different sized pools. For example, if you require a large number of DarkRiftReaders to exist concurrently on a thread you could increase the `maxCachedReaders` value to ensure there is enough instances in the pool to avoid reallocating and dereferencing each time.

Customization should be avoided unless you are sure it will help since overallocating will result in wasted memory and underallocating can severely degrade performance and add unecessary GC pressure. To get the best results you should be using a profiler.

It is important to remember that changes to customization may not take effect if multiple servers/clients are or have been in use this is because the object caches are unique to each thread not to each server/client. When the server initializes, it attempts to set the parameters of the object caches but will not be able to if another initialization (e.g. from another client) has taken place since threads are shared between them. You can see in server logs if this has occured; there is currently no way to see the result in the client.
