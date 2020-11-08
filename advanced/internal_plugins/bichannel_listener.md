# Bichannel Listener
The `BichannelListener` (accessed by `AbstractBichannelListener`) in the `DarkRift.Server.Plugins.Listeners.Bichannel` namespace is a network listener that operates over TCP (for reliable traffic) and UDP (for unreliable traffic) concurrently.

## Settings
The following settings are exposed to configuration:

| Setting   | Default | Description |
|-----------|---------|-------------|
| `address` (Obsolete) |  | The IP address to listen on. Use the `address` property on the `listener` element instead. |
| `port` (Obsolete) |  | The port to listen on. Use the `port` property on the `listener` element instead. |
| `udpPort` | Same as port | The UDP port to listen on, if different from the listener port. |
| `noDelay` | false | Whether to disable Nagel's algorithm on the TCP channel. |
| `maxTcpBodyLength` | 65535 | The maximum payload size allowed over TCP, in bytes. |

The `maxTcpBodyLength` setting is there to stop arbitrary sized memory allocation by remote clients crashing the server. This value should only be set as high as you need as setting to absurdly high values can open your server up to easy denial of service attacks.
