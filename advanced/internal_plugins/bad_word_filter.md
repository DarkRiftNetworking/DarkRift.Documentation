# Bad Word Filter
The `BadWordFilter` (accessed through `IBadWordFilter`) in the `DarkRift.Server.Plugins.Chat` namespace provides a chat filtering utility in DR Pro.

## Settings
The following settings are exposed to configuration:

| Setting   | Default | Description |
|-----------|---------|-------------|
| `replacementSymbols` | `$#@%&*!` | The set of characters to use when performing a `FilterToSymbols` operation. |
| `url` | `https://darkriftnetworking.com/DarkRift2/Resources/BadWords.xml` | The URL to source filtered words from. |

## Commands
The following commands are exposed:

| Command   | Usage | Description |
|-----------|-------|-------------|
| `badwordfilter` | `badwordfilter update` | Allows you to make changes to the bad word filter. |
