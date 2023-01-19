# Commands
Plugins can accept commands in a similar way to the sniffer and plugins controller does (in fact, since they are implemented as plugins it's exactly the same way). This guide will show you how that can be done with your own plugins.

To add commands to the server we must specify them as an overridden property, similar to how Name and Version are defined. In your plugin class you should add code similar to:
```csharp
public override Command[] Commands => new Command[]
{
    new Command("HighScores", "Retreives the high scores from the game.", "Highscores (-limit=<count>)", HighScoresCommandHandler)
};
```
All we do here is override the default command list (which is empty) with our own. In each command entry we specify: the name of the command (what you type in), a description and command usage string to be shown in the help command, and lastly a method that should be called when the server administrator types in this command. The command name is case insensitive however arguments are not.

The handler for the command should look similar to:
```csharp
void HighScoresCommandHandler(object sender, CommandEventArgs e)
{
    
}
```
The CommandEventArgs holds the arguments passed with the command and provides a few methods to access them.
Flags, with or without values (e.g. `-i` and `-i=10`) can be found in the Flags property which will give you a NameValueCollection where the value is null if it wasn't specified with an argument.

Arguments will contain any non-flag like arguments (e.g. `uninstall`) in the order they were defined.

You can also get the original command passed in and the raw arguments all together.

There are no extra steps to adding the new commands to the `help` command since this is automatically generated at search time. Hence, you should be able to immediately run:

`help highscores`

And you should see the help strings you added earlier.

Should you need to indicate to the user that a syntax error has occured then you should throw a CommandSyntaxException and %DarkRift will show the user the usage text set in the command and any message you supply.
