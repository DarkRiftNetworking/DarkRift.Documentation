# Logging Element
`<logging>` element is used to specify a logging settings for the server.

## Attributes
| Attribute | Values | Description |
|-----------|--------|-------------|
| startupLevels | Comma separated list of: trace, info, warning, error, fatal | The log levels that should be output to the console during startup, before log writers have been loaded. |

## Child Elements
- logWriters
