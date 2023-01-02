# Port Clashes
## Example
```
Unhandled Exception: System.Reflection.TargetInvocationException: Exception has been thrown by the target of an invocation. ---> System.Net.HttpListenerException: Failed to listen on prefix
'http://localhost:9796/' because it conflicts with an existing registration on the machine.
   at System.Net.HttpListener.AddAllPrefixes()
   at System.Net.HttpListener.Start()
   at %DarkRift.Server.Plugins.Metrics.Prometheus.PrometheusEndpoint..ctor(MetricsWriterLoadData metricsWriterLoadData)
   ...
```

```
Unhandled Exception: System.Net.HttpListenerException: Failed to listen on prefix 'http://localhost:10666/' because it conflicts with an existing registration on the machine.
   at System.Net.HttpListener.AddAllPrefixes()
   at System.Net.HttpListener.Start()
   at %DarkRift.Server.Plugins.HealthCheck.HttpHealthCheck.Loaded(LoadedEventArgs args)
   at %DarkRift.Server.ExtendedPluginManagerBase`1.Loaded()
   at %DarkRift.Server.DarkRiftServer..ctor(ServerSpawnData spawnData, ClusterSpawnData clusterSpawnData)
   at %DarkRift.Server.DarkRiftServer..ctor(ServerSpawnData spawnData)
   at %DarkRift.Server.Console.Program.Main(String[] args)
```

## Description
This exception is thrown when starting the server when another %DarkRift server is already running. A common mistake is to change the port the network listener is running on in order to avoid port clashes across multiple %DarkRift servers on the same machine, however %DarkRift also has plugins that listen on additional ports to provide additional services such as the HTTP health check and the Prometheus metrics listener.

The stack trace of the exception usually shows which plugin is unable to load.

## Solutions
### Change the Ports that are Clashing
To change the port you should consult the documentation for the specific plugin that is unable to load. Examples for the HTTP health check and Prometheus metric writer are:
```xml
<plugins>
  <plugin type="HttpHealthCheck">
    <settings port="10667" />
  </plugin>
</plugins>
```

```xml
<metrics>
  <metricsWriter type="PrometheusEndpoint">
      <settings port="9797" />
  </metricsWriter>
</metrics>
```

A helpful note to remember is that these can be passed in from environment variables or command line arguments with configuration variables:
```xml
<plugins>
  <plugin type="HttpHealthCheck">
    <settings port="$(HEALTH_CHECK_PORT)" />
  </plugin>
</plugins>
```

### Disabling the Plugins that are Clashing
It is also possible to disable the plugins that are unable to load if you are not relying on their functionality:
```xml
<plugins>
  <plugin type="HttpHealthCheck" load="false" />
</plugins>
```

The Prometheus endpoint plugin is only loaded when explicitly defined in configuration so removing its configuration is sufficient to stop it loading.
