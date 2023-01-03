# Configuration Variables
It can be necessary to setup server parameters at runtime when the server is started rather than when writing the configuration files. To do this you can use configuration variables that are passed in as command line arguments.

## Defining a Configuration Variable
To define something to use a variable in the configuration you simply need to use the `$(variableName)` syntax. This can be placed in the attribute alone or can be combined with other text, for example:
```xml
<database name="$(dbName)" connectionString="Data Source=$(dbFile);Version=3;"/>
```
When the configuration file is loaded the variables will be resolved to the database name and location you provide.

Variable names can be any combination of letters, numbers and some symbols such as `_`.

## Passing Variables at Runtime
To set the value of variables you can either pass them a flags when you invoke the server executable, for example:
```bash
%DarkRift.Server.Console -dbName=MyDB -dbFile="C:/my_db.db"
```
Or provide them as environment variables.

It is worth noting that you can also pass in an alternative configuration file if more customisation is needed:
```bash
%DarkRift.Server.Console "AlternativeServer.config" -dbName=MyDB -dbFile="C:/my_db.db"
```
