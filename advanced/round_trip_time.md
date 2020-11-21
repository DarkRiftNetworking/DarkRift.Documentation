# Round Trip Time
Calculating the time it takes to send data between client and server is a critical part of many applications and is fundamental for all lag compensation code in games. DarkRift has some built in functionality for calculating the round trip time and ping between a client and the server.

## Introduction
Round trip time is calculated by sending a 'ping' from host `A` to `B` and, on reception at `B`, returning an acknowledgment from `B` to `A`. By timing this interaction `A` can calculate the 'round trip time', and from that estimate the 'ping time' by halving it.

To aid this DarkRift provides some additional fields in the `Message` type that allow a message to be sent as a 'ping' or 'ping acknowledgment' message. Furthermore, DarkRift will automatically time these messages and process the resulting times to smooth them.

It's important to note that DarkRift does not send these messages for you. Instead, any message can be converted to a ping message or ping acknowledgment so that you can use messages you already send to calculate the round trip time thus alleviating the need for additional messages to be sent just for this calculation.

Good candidates for conversion to ping messages are request-response type messages (where the response is generated quickly and can be used as the ping acknowledgment message), and messages that are sent regularly and consistently such as those sent from the main update loops.

## Calculating the Round Trip Time
To make a message a ping message you simply need to call `Message.MakePingMessage()`:
```csharp
using (Message message = Message.Create(writer))
{
    message.MakePingMessage();
    ...
    client.SendMessage(message);
}
```

Calling `Message.MakePingMessage()` adds two additional bytes to the message that contain a 16 bit identifier for the ping message. This means that a ping acknowledgement message belongs uniquely to a ping message and cannot acknowledge the wrong ping message.

When sending the message the identifier will be automatically be stored with the time stamp at which it's send.

Upon receiving this message you should check if it is in fact a ping message and return a response as quickly as possible to avoid adding unnecessary time into the calculation.

To do this you can use the `IsPingMessage` property and the `MakePingAchnowledgementMessage(Message acknowledging)` method.
```csharp
using (Message receivedMessage = args.GetMessage())
{
    if (receivedMessage.IsPingMessage)
    {
        ...
        using (Message acknowledgementMessage = Message.Create(writer))
        {
            acknowledgementMessage.MakePingAcknowledgementMessage(receivedMessage);
            ...
            client.SendMessage(acknowledgementMessage);
        }
    }
    ...
}
```

When the ping acknowledgement is received the timer will be stopped and the round trip time recorded automatically.

## Using the Round Trip Time
Both `IClient` and `DarkRiftClient` have an `RoundTripTime` property which contains both the `LatestRtt` and a `SmoothedRtt` which is a moving average of the most recent round trip times. The `SmoothedRtt` should be used if you want to reduce the weight of anomalous results at the cost of a slight delay.

```csharp
Logger.Info($"Latest RTT was {client.RoundTripTime.Rtt} and the smoothed RTT was {client.RoundTripTime.SmoothedRtt} over {client.RoundTripTime.RttSampleCount} samples.");
```
