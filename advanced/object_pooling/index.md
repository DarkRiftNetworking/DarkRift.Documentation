# Object Pooling
DarkRift, in order to reduce GC pressure and to improve performance, recycles objects where possible to avoid unnecessary allocations. This section explains how that pooling works, what objects are pooled and how to use the low level interface provided to cache your own objects.
