# HTTP Health Check
The `HttpHealthCheck` exposes a HTTP endpoint that can be queried to get the server's current status for health checks.

If the server is available a HTTP 200 response will be returned with a JSON payload containing more information:
```json
{
    "listening": true,
    "startTime": "2020-11-08T14:20:00T",
    "type": "Pro",
    "version": "2.8.1"
}
```

## Settings
The following settings are exposed to configuration:

| Setting   | Default | Description |
|-----------|---------|-------------|
| `host` | `localhost` | The host to listen on. |
| `port` | `10666` | The port to listen on. |
| `path` | `/health` | The sub-path to listen on. |
