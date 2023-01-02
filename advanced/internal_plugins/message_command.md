# Message Command
The `MessageCommand` sends a message to a client. This is mostly used for internal development of %DarkRift.

## Commands
The following commands are exposed:

| Command   | Usage | Description |
|-----------|-------|-------------|
| `message` | `message <client> <sendMode> <tag> <data> <data> <data> ...` | Sends messages for testing the client. |

The message data must be specified as the raw payload bytes.
