# Debug Command
The `DebugCommand` displays debug information about the %DarkRift Server. This is mostly used for internal development of %DarkRift.

## Commands
The following commands are exposed:

| Command   | Usage | Description |
|-----------|-------|-------------|
| `debug-set` | `debug-set -<property-name>=<value>|-<property-name>...` | Sets a property in the server. |
| `debug-get` | `debug-get -<property-name>|-<property-name>...` | Gets a property in the server. |
| `debug-rtt` | `debug-rtt` | Shows the current round trip time to each client. |
| `debug-logs` | `debug-rtt (-l)` | Prints a number of log lines at different levels. |

The `debug-set` and `debug-get` commands only accept the `events-from-dispatcher` property.

The `debug-logs` command can be useful for testing log writers. The `-l` property emits messages using the obsolete `WriteEvent` method and is only used for internal development.
