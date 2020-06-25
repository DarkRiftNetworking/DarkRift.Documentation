# Health Checks
Health checks provide an indication as to whether a server is working and is deemed 'healthy' by a system. They can be used to automatically restart failed services, ensure a certain deployment size or generally get state about an application.

## Health Checks in DarkRift
While it is possible to test if a DarkRift server is ready for connections by connecting a TCP socket to its endpoint this creates state in DarkRift on some listeners and hence is not recommended. Instead you can use a HTTP health check and the `HttpHealthCheck` plugin.

If you have `loadBydefault` disabled for plugins in configuration, or wish to specify extra properties to the plugin you can explicitly declare it:
```xml
<plugins loadByDefault="false">
  <plugin type="HttpHealthCheck" load="true">
    <settings
      port="10666"
      host="localhost"
      path="/health" />
  </plugin>
</plugins>
```
The properties have the following defaults:
- `port` - defaults to `10666`
- `host` - defaults to `localhost`
- `path` - defaults to `/health`

When queried with a `GET` request, a JSON response will be returned with a `200 OK` status if the server is running and accepting connections:
```json
{"listening": true, "startTime": "2020-02-17T23:29:59.620Z", "type": "Pro", "version": "2.5.0.0"}
```

If the server is not running it will fail to respond; if it is running but not operational it will return a `503 Service Unavailable` with a similar JSON body to the above.
