# Setting up Prometheus Metrics
DarkRift includes a [Prometheus](https://prometheus.io/) metrics writer that provides a Prometheus compatible end point. The `PromethusEndpoint` plugin makes all metrics in DarkRift available for graphing directly in Prometheus or in other applications such as [Grafana](https://grafana.com/) and allows more intelligent auto-scaling in orchestration solutions such as [Kubernetes](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/).

## Configuring the Plugin
The `PrometheusEndpoint` plugin can be configured as the server's metrics writer using the below configuration:
```xml
  <metrics>
    <metricsWriter type="PrometheusEndpoint">
      <settings
        host="localhost"
        path="/health"
        port="10666" />
    </metricsWriter>
  </metrics>
```

The following parameters can be set in the plugin's settings:

| Name   | Description                                              | Default   |
|--------|----------------------------------------------------------|-----------|
| `host` | The host the Prometheus endpoint will be started on.     | localhost |
| `path` | The subpath the Prometheus endpoint will be started on.  | metrics   |
| `port` | The port the Prometheus endpoint will be started on.     | 9796      |

## Using Prometheus Specific Extensions
The DarkRift metrics system only provides a limited subset of Prometheus functionality in order to maintain support for other metrics systems. Some extensions methods for `MetricsCollector` are provided in the `DarkRift.Server.Plugins.Metrics.Prometheus` that will allows you to set bucket sizes explicitly for histograms and more.

Note: As DarkRift targets .NET 3.5 we are not yet able to provide Prometheus support via the official .NET client, hence the two systems will not work well together.
