# Message and Mock
To isolate code on the client and server for testing you can use the <command>message</command> and the <command>mock</command> commands.
## Message
The `message` command allows you to send a message from the server to the client in order to test the client's behaviour when receiving that message. This can also be used to test basic connectivity issues.

To send a message you specify the recipient, send mode and tag followed by the data as arguments. For example `message 0 unreliable 10 00 00 00 00`
        
## Mock      
The `mock` command allows you to inject a fake message as if it was received at the server in order to test the server's behaviour when receiving that message. This operates in a similar way to `message`.

To mock a message again you specify the sender, send mode tag and data as arguments. For example: `mock 0 unreliable 10 00 00 00 00`
