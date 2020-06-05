# Log Writer Element
`<logWriter>` element is used to specify a log writer to be loaded into the server.
      
## Attributes
| Attribute | Values | Description |
|-----------|--------|-------------|
| name (required) |  | The unique name to give the log writer. |
| type (required) |  | The name of the log writer type to instantiate. |
| levels (required) | Comma sperated list of: trace, info, warning, error, fatal | The log levels that should be sent to this writer. |

## Child Elements
- settings
