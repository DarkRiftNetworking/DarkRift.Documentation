# Debugging
There are a number of ways to debug DarkRift plugins which will moke solving issues much faster for you. This will guide you through using some of them

Firstly, we discussed it earlier but the sniffer is a good way of telling you what data is getting to the server and what header data it contains. If you think a message isn’t reaching the server then check with the sniffer!

Secondly you can use most .NET debuggers with DarkRift. If you copy the .pdb file that’s output with your plugin into the plugins directory with your plugins library, you can attach your IDE’s debugger to the DarkRift hosting process and then debug your code as you would any other code.

DarkRift also has two other helpful commands: `message` and `mock`. These allow you to send messages from the server and simulate receiving messages from a client respectively so that you can test your client and server code before actually implementing the other. These are very helpful for isolating code problems (and connectivity problems!) and could also be used to help with integration testing.

Lastly, if all else fails, just print stuff to the console (see Logging)!
