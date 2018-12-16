# Server Element
The `<server>` element is used to define global server parameters and parameters related to the listener sockets.

## Attributes
| Attribute | Values | Description |
|-----------|--------|-------------| 
| address (required) | Any valid IP address | The IP address to bind the listen sockets on. |
| port (required) | 0-65535 | The port number to bind the listen sockets on. |
| ipVersion (required) | IPv4, IPv6 | Whether to bind the listen sockets on IPv4 or IPv6. |
| noDelay | true, false | Whether Nagle's algorithms should be disabled on TCP. |
| maxStrikes (required) |  | The maximum number of strikes a client can be given before they're automatically disconnected from the server. |
| useFallback | true, false | Whether the fallback networking system should be used. This is necessary for Unity compatibility. |

## Child Elements
There are no child elements for this element.
