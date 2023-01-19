# Mock Command
The `MockCommand` simulates receiving a message from a client. This is mostly used for internal development of %DarkRift.

## Commands
The following commands are exposed:

| Command   | Usage | Description |
|-----------|-------|-------------|
| `mock` | `mock <client> <sendMode> <tag> <data> <data> <data> ...` | Mocks message receiving for testing plugins. |

The message data must be specified as the raw payload bytes.
