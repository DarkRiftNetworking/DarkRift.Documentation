# Recycling Warnings
## Example
```
10 DarkRiftWriter objects were finalized last period. This is usually a sign that you are not recycling objects correctly.
```

## Description
This warning is raised when DarkRift detects objects that should be recycled are being finalized (i.e. garbaged collected) when they shouldn't be. This usually has no functional impact on the application but can indicate an opportunity to improve performance.

The following snippet would cause this warning to be displayed:
```csharp
DarkRiftWriter writer = DarkRiftWriter.Create();
// Do something here with the writer
// Forget to dispose of it
```

## Solutions
### Fix the Recycling Issue
Fixing the root recycling issue is the best solution to fix this warning, however there is no easy way of finding which of the specific object usages are causing the warning to be displayed as DarkRift does not have this information to provide to you. You will need to search through all usages of the particular object with the issue manually.

To fix the snippet above, the writer should be wrapped in a `using` statement:
```csharp
using (DarkRiftWriter writer = DarkRiftWriter.Create())
{
    // Do something here with the writer
}
```

A `using` statement is preferred to simply calling `Dispose()` as it also recycles the object when an exception occurs.

### Suppress the Warning
`ObjectCacheMonitor` plugin is responsible for producing this warning and therefore the warning can be suppressed by simply disabling the plugin:
```xml
<plugins>
  <plugin type="ObjectCacheMonitor" load="false" />
</plugins>
```
