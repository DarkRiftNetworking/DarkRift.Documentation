# Cache Element
The `<cache>` element is used to specify settings for the object cache.
      
## Attributes
| Attribute | Values | Description |
|-----------|--------|-------------|
| maxCachedWriters | | The maximum number of DarkRiftWriter instances stored per thread. |
| maxCachedReaders | | The maximum number of DarkRiftReader instances stored per thread. |
| maxCachedMessages | | The maximum number of Message instances stored per thread. |
| maxCachedSocketAsyncEventArgs | | The maximum number of SocketAsyncEventArgs instances stored per thread. |
| maxActionDispatcherTasks | | The maximum number of ActionDispatcherTask instances stored per thread. |
| maxAutoRecyclingArrays | | | The maximum number of AutoRecyclingArray instances stored per thread. |
| extraSmallMemoryBlockSize | | The number of bytes in the extra small memory bocks cached. |
| MaxExtraSmallMemoryBlocks | | The maximum number of extra small memory blocks stored per thread. |
| smallMemoryBlockSize | | The number of bytes in the small memory bocks cached. |
| maxSmallMemoryBlocks | | The maximum number of small memory blocks stored per thread. |
| mediumMemoryBlockSize | | The number of bytes in the medium memory bocks cached. |
| maxMediumMemoryBlocks | | The maximum number of medium memory blocks stored per thread. |
| largeMemoryBlockSize | | The number of bytes in the large memory bocks cached. |
| maxLargeMemoryBlocks | | The maximum number of large memory blocks stored per thread. |
| extraLargeMemoryBlockSize | | The number of bytes in the extra large memory bocks cached. |
| maxExtraLargeMemoryBlocks | | The maximum number of extra large memory blocks stored per thread. |

## Child Elements
There are no child elements for this element.
