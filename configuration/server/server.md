# Server Element
The `<server>` element is used to define global server parameters and parameters related to the listener sockets.

## Attributes
| Attribute | Values | Description |
|-----------|--------|-------------|
| address (deprecated) | Any valid IP address | The IP address to bind the listen sockets on. Use listener configuration instead. |
| port (deprecated) | 0-65535 | The port number to bind the listen sockets on. Use listener configuration instead. |
| ipVersion (deprecated) | IPv4, IPv6 | Whether to bind the listen sockets on IPv4 or IPv6. Use listener configuration instead. |
| noDelay (deprecated) | true, false | Whether Nagle's algorithms should be disabled on TCP. Use listener configuration instead. |
| maxStrikes (required) |  | The maximum number of strikes a client can be given before they're automatically disconnected from the server. |
| useFallback (deprecated) | true, false | Whether the fallback networking system should be used. Use listener configuration instead. |

## Child Elements
There are no child elements for this element.
