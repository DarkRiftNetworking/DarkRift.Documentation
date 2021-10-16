# Tuning Object Pools
Object pool tuning is an important step when optimizing the performance of a DarkRift server, but can be safely ignored if performance isn't key. This section talks about the different tuning options available and how to set each one.

## Object Pool Settings
Object pools are normally configured through code but can also be configured using the server configuration for ease of access. See the `cache` element for more information on tuning object caches from configuration, the information here is largely transferable.

The following properties can be set on the object caches:
- `Max Instances` - the number of instances that will be pooled in the thread local pool.
- `Max Instances Balancing Pool Multiplier` - the multiplier of Max Instances that determines the number of instances that will be pooled in the balancing pool, for example a multiplier of 4 and a Max Instances of 8 would mean the balancing pool can store 32 instances.
- `Rebalance Ratio` - the fraction of Max Instances that will be transfered to and from the balancing pool during a rebalance, for example a ratio of 0.5 and a Max Instances of 8 would mean 4 instances are transfered during a rebalance.

The following properties can be set on the collection caches for each size of object extra small, small, medium, large, extra large (where `___` can be replaced with the object size):
- `Max ___ Instances` - the number of instances that will be pooled in the thread local pool.
- `Max ___ Instances Balancing Pool Multiplier` - the multiplier of Max Instances that determines the number of instances that will be pooled in the balancing pool, for example a multiplier of 4 and a Max Instances of 8 would mean the balancing pool can store 32 instances.
- `___ Rebalance Ratio` - the fraction of Max Instances that will be transfered to and from the balancing pool during a rebalance, for example a ratio of 0.5 and a Max Instances of 8 would mean 4 instances are transfered during a rebalance.
- `___ Instance Size` - the minimum number of elements in a collection of this size, for example an instance size of 16 would mean it pools instances of size 20 but not instances of size 10.

For clarity, a collection cache can be thought of as five separate object caches where each serves a different size object.

## Tuning Object Pools
Tuning of each property is largely the same between object caches and collection caches.

Max instances properties should be a little higher than the maximum number of instances that can be simultaneously out of the pool divided by the number of threads that are active (since this value is per thread). This can sometimes be difficult to estimate so using a profiler to look for allocations (after a warm up period to populate the pool) can help, allocations are a sign this property needs increasing. Setting this property too high is not usually an issue as it will only affect memory use.

Max instances balancing pool multiplier properties should be set higher when the pools are likely to rebalance frequently (such as thread starting/stopping frequently) as this will allow more instances to be stored in the balancing pool between the threads. Setting this property too high is not usually an issue as it will only affect memory use.

Rebalance Ratio properties depend on the behaviour of the objects across threads. If instances change threads in a strictly producer/consumer pattern setting this closer to 1 is a good idea to reduce the number of rebalances required, if instances should change threads evenly or very little at all a lower value closer to 0.75 is likely better.

Instance size properties for collection caches should reflect the sizes of the collections being used. The extra large collection instance size should be set to a size that catches the largest collections that should be cached but causes overly large collections that are unlikely to be reused frequently to be discarded.
