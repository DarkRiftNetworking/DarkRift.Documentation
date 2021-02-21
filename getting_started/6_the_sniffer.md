# The sniffer
Let’s take some time out from the coding to look at one of DarkRift’s most powerful **debugging** features.
    
## Sniffing Data
Currently DarkRift is just sitting there, occasionally telling you when someone connects or disconnects, what if I want to know if it actually receives a message I send?

**Restart** your **server** and type into it `sniffer add -a`.

Now when you test you should see **every message** that **enters** the server get printed to the console window!

Since that’s a mess of information lets narrow it down. Firstly though, you can use `sniffer remove <flags>` to remove a **previously** setup sniffer **rule**, for example `sniffer remove -a`.
        
There are a number of filters you can use with the sniffer:

| Filter | Description | Example |
|--------|-------------|---------|
| `-t` | Sniffs for a specified tag | `-t=9`
| `-ip` | Sniffs for a specified IP address | `-ip=10.56.81.2` |
| `-id` | Sniffs for a specified client | `-id=10` |

You can specify **multiple filters** at the same time and they will logically **AND** together (i.e. a message has to satisfy **both filters** to be printed) and you can add multiple sniffing rules and they will logically **OR** (i.e. a message will be printed if it satisfies **any** defined sniffing rule).

Additionally, you can specify the `-h` flag to see that data output in **hexadecimal**. Though be wary if you have large messages because it might get hard to read!

Have a play with it in some of the example scenes; you probably won’t be able to have quite as much fun with it in our tutorial game since we only ever send one tag from the clients!

For those of you who knew the original system in DarkRift 1 hopefully you’ll appreciate how much better this is!
