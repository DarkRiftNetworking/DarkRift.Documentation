# Using the Metrics System
%DarkRift's metrics system exposes a number of key metrics about the server and its plugins to be graphed or alerted on by external applications. By default, %DarkRift comes with support for [Prometheus](https://prometheus.io/) but the system can be extended easily to support other metrics systems.

Before metrics will be reported, a metrics writer needs to be configured.

The metrics system can be accessed by plugins using the `MetricsCollector` type which provides a number of factory methods for different metric types.

## Counter Metrics
Counter metrics always increase in value and are usually used to count when a discrete event occurs, for example a message being sent.
```csharp
private readonly ICounterMetric myCounter;

public MyPlugin(PluginLoadData pluginLoadData) : base(pluginLoadData)
{
    myCounter = MetricsCollector.Counter("my_event", "The number of times my event has occurred.");
}

public void MyEventHasOccurred()
{
    myCounter.Increment();
}
```

## Gauge Metrics
Gauge metrics are set to a specific value and can increase or decrease, for example a gauge is used to report the current number of clients.
```csharp
private readonly IGaugeMetric myGauge;

public MyPlugin(PluginLoadData pluginLoadData) : base(pluginLoadData)
{
    myGauge = MetricsCollector.Gauge("my_number", "The number it is currently set to.");
}

public void MyNumberHasChanged(double value)
{
    myGauge.Report(value);
}
```

## Histogram Metrics
Histogram metrics show the distribution of a set of values and are commonly used to report timings, for example the time it takes for an event to complete.
```csharp
private readonly IHistogramMetric myHistogram;

public MyPlugin(PluginLoadData pluginLoadData) : base(pluginLoadData)
{
    myHistogram = MetricsCollector.Histogram("my_timing", "The time taken to do an operation.");
}

public void MyOperationCompleted(double elapsedTime)
{
    myHistogram.Report(elapsedTime);
}
```

## Using Tags
Tags, sometimes called labels in other systems, can be used to categorize a metric into more specific subgroups.

In %DarkRift this is done by specifying the set of keys when creating the metric and then providing a set of values for those tags before being able to use the metric.
```csharp
private readonly TaggedMetricBuilder<IHistogramMetric> myHistogram;

public MyPlugin(PluginLoadData pluginLoadData) : base(pluginLoadData)
{
    myHistogram = MetricsCollector.Histogram("my_timing", "The time taken to do an operation.", "my_first_tag_key", "my_second_tag_key");
}

public void MyOperationCompleted(double elapsedTime)
{
    myHistogram.WithTags("first_value", "second_value").Report(elapsedTime);
}
```
For performance reasons you can store and reuse the result of the call to `WithTags`.

## Internal Metrics
%DarkRift reports a number of internal metrics automatically however, to ensure performance, metrics that would be emitted per message are disabled by default. These can be enabled using the `enablePerMessageMetrics` option:
```xml
<metrics enablePerMessageMetrics="true">
  <metricsWriter type="PrometheusEndpoint"/>
</metrics>
```
