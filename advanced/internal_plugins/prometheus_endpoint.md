# Prometheus Endpoint
The `PrometheusEndpoint` is a metrics writer providing a Prometheus compatible endpoint of the server's metrics.

## Settings
The following settings are exposed to configuration:

| Setting   | Default | Description |
|-----------|---------|-------------|
| `host` | `localhost` | The host to listen on. |
| `port` | `10666` | The port to listen on. |
| `path` | `/health` | The sub-path to listen on. |
