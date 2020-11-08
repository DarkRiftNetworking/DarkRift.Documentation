# Client Command
The `ClientCommand` allows you to create mock clients on a server and is commonly used with the `message`/`mock` commands. This is mostly used for internal development of DarkRift.

## Commands
The following commands are exposed:

| Command   | Usage | Description |
|-----------|-------|-------------|
| `client` | `client add (-ip=<ip>) (-port|p=<port>) (-h)\nclient remove <id>`</br>`client remove <id>` | Creates mock clients for testing. |
