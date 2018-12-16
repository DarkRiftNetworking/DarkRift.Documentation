# Installs and Upgrades
Often your plugins will require database tables and other things to be setup in order to work properly, it might be tempting to put these in the constructor but the problem with building all our tables in the constructor is that it will run every time DarkRift loads our plugin, which hence causes delays on startup. That’s not a problem for some applications when servers won’t be starting up and stopping regularly but what if we allocate a server instance for each room? That now adds delay to the start of each game while we needlessly connect to the database server to establish everything is already setup!

## Installation
To solve this DarkRift (Pro only here!) has a plugin installation system. Things like setting up DB tables, which only need to be done when we first run the plugin, can go in here and DarkRift will ensure it’s only called on that first run.

To do this we can override the virtual method in Plugin:
```csharp
protected override void Install(InstallEventArgs args)
{
}
```
This overrides the default installation routine (which does nothing) so you can supply our own logic, such as:
```csharp
protected override void Install(InstallEventArgs args)
{
    using (DbConnection db = OpenDatabaseConnection())    //Open some arbitrary database connection from somewhere
    {
        db.Open();
        using (DbCommand command = db.CreateCommand())
        {
            command.CommandText = "CREATE TABLE IF NOT EXISTS Scores (id INTEGER PRIMARY KEY AUTOINCREMENT, score INTEGER)";
            command.ExecuteNonQuery();
        }
    }
}
```
And, in true DarkRift fashion, that’s all there is to it!

## Upgrades
But what happens if you need to change something you’ve already setup? If you change the version of your plugin (as specified on the properties we overrode at the start) then DarkRift will detect this and invoke the Upgrade method on your plugin so you can make the necessary changes without having to resort to checks in the constructor again!

To demonstrate this, let’s say we want to change the previously declared scores table to have a username field as well:
```csharp
protected override void Upgrade(UpgradeEventArgs args)
{
    using (DbConnection db = OpenDatabaseConnection())
    {
        db.Open();
        using (DbCommand command = db.CreateCommand())
        {
            command.CommandText = "ALTER TABLE scores ADD username TEXT";
            command.ExecuteNonQuery();
        }
    }
}
```
Do remember to change the version parameter to something higher for this to work however.

You can also query the arguments object to see which version was previously installed so you can ensure that you upgrade everything necessary even if you’re upgrading across multiple versions.

## Loaded
It's worth a quick note here that there is one other event, `Loaded`, which is called when the server has finished loading.

You may have found that you can't get and cache a plugin reference from the constructor as it throws an exception; this is because the order plugins are loaded in is not specified so there is no guaranetee the server knows about the plugin you're requesting yet.

Instead, this can be done in the `Loaded` event which guarantees all plugins have been loaded but the server will still not have been started yet so you know no other events will fire beforehand.
