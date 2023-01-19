# Writing Custom Metrics Writers
It is possible to write you own metrics writers if you want to use another metrics system not currently supported by %DarkRift. Doing so simply requires you to create a `MetricsWriter` plugin and provide concrete implementations of the appropriate metric interfaces.

## MetricsWriter
The `MetricsWriter` is a type of plugin that provides %DarkRift with a specific implementation for the metrics system. The `MetricsWriter` class is responsible for providing concrete implementations of the `ICounterMetric`, `IGaugeMetric` and `IHistogramMetric` interfaces.

When providing implementations of tagged metrics there is an intermediate step that is performed through the `TaggedMetricBuilder` to get an implementation with specific values for the tags. The `TaggedMetricBuilder` simply requires a function that creates the specific metric instance when called with the values of the tags.

## Considerations
Where possible, no garbage should be generated once a metric instance has been created as this may interfere with performance.

If two metrics are created with the same properties (except for the description property which is considered only informational) the two metrics should operate as the same metric. For example, incrementing a counter once on each instance should result in the metric showing two increments.
