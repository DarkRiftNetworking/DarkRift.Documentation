# Matchmaking
%DarkRift Networking includes a matchmaker plugin for games where players need to be grouped into 'rooms' according to certain metrics. This page will guide you through using the matchmaker.

Note, the matchmaker is only included with the Pro version.

## Matchmaker Setup
The %DarkRift matchmaker is implemented as an abstract plugin in the %DarkRift.Server.Plugins.Matchmaking namespace. This means to use it you must inherit from it to provide it some basic implementation details including a ranking function and a type parameter.

The type parameter is required to set the type of object the matchmaker will be operating on. The matchmaker can match groups of any object type as some users may want to simply match IClients while some users may wish to match on their own player representations etc. The objects being matched by the matchmaker are known in %DarkRift as 'entities' to generalise them from their actual type or purpose.

The ranking function is a method taking 2 entities and providing a value between 0 and 1 (inclusive) to rate how good a match between them would be. For example, if they would be perfect in a group together it would return nearer 1, if they were OK for each other it might return 0.25-0.75 and if they were an appauling match with each other it would return nearer 0.

The following forms a very simple matchmaker that would match based on player's levels where the max level they can reach is 100.
```csharp
public class ExampleMatchmaker : RankingMatchmaker<int>
{
    public override bool ThreadSafe => true;

    public override Version Version => new Version(1, 0, 0);

    public ExampleMatchmaker(PluginLoadData pluginLoadData) : base(pluginLoadData)
    {
    }

    public override float GetSuitabilityMetric(int entity1, int entity2, MatchRankingContext<int> context)
    {
        return Math.Abs(entity1.Level - entity2.level) / 100f;
    }
}
```
Lastly, in order for the matchmaker to work you need to specify a number of settings in the configuration file. Add the following to the plugins element:
```xml
<plugin type="ExampleMatchmaker" load="true">
  <settings discardThreshold="0.2"
            groupDiscardThreshold="0.2"
            entitiesPerGroup="4"
            tickPeriod="500" />
</plugin>
```
Since some of these options are quite complex they are documented in detail in a section below.

## Using the Matchmaker
The matchmaker is accessible from your plugins like any other plugin is. Once you have a reference you can using the IMatchmaker interface to abstract the type of matchmaker away from your plugin.

The IMatchmaker interface provides the basic Enqueueing methods and the GroupFormed event which will be invoked everytime a satisfactory group is found by the matchmaker. The enqueue methods also accept an individual callback and return an IMatchmakerQueueTask which allows you to query the state of the entities you queued, subscribe to events relating only to those entities, or cancel the matchmaking for that group.

A typical queue operation might look like the following where PlayerMatchmakingStateChangedHandler sends a message to inform the player about his status in the queue.
```csharp
IMatchmakerQueueTask<Player> task = matchmaker.Enqueue(
    player,
    MatchmakingStateChangedHandler
);
```
It is also perfectly possible to queue groups of entities with very few changes to the code since individuals are simply considered in the matchmaker as a group of one.
```csharp
IMatchmakerQueueTask<Player> task = matchmaker.EnqueueGroup(
    players,
    MatchmakingStateChangedHandler
);
```

## Matchmaker Configuration In Detail
The matchmaker has a number of settings that must be configured in the Server.config file: discardThreshold, groupDiscardThreshold, entitiesPerGroup, and tickPeriod.

The discard threshold is a useful tuning parameter that allows you to prune large parts of the search tree. If any two entities have a suitability metric beneath the discard threshold then they will not be put in a group together. By setting this value higher you prune a larger portion of the search tree and hence the matchmaker will use less resources but players will have less potential matches, setting this value lower prunes less of the search tree and will have a a higher resource cost but players will have more potential matches. With more potential matches you are likely to see faster but less high quality matchmaking.

The group discard threshold operates similarly to the discard threshold but acts on the average suitability metrics between all players in the two groups being considered. If two groups are being considered to be matched and all players report suitability metrics above the discard threshold then those groups may be matched together, however if all returned suitability metrics were low (but just high enough over the discard threshold) the two groups may not be good matches. This group discard threshold allows those to be discarded to further prune the search tree.

Setting the group discard threshold lower than the discard threshold will cause it to have no effect.

The entities per group setting indicates how many entities there will be in the groups outputted.

The tick period is the time in milliseconds between matchmaker searches. Each tick the matchmaker will try to make as many groups as possible and when no more groups can be made it will wait until the next tick before it tries again. If a search is already in progress the tick will be skipped over.

Setting the tick period to be higher will allow more time for users to queue up for each search but could increase waiting times, setting the period lower may reduce the waiting times but there will be less users to match against so it could result in lower quality matches.

Balancing these values is essential to good matchmaking and will depend heavily on the rate entities queue and the tolerances allowable between suitability metrics.

## Matchmaker Ranking Builder
To assist in creating a good ranking function %DarkRift provides the MatchmakerRankingBuilder. This class provides a set of methods for the common computations you might need to perform within a builder pattern-style interface.

When building the ranking the builder takes in weights for each metric you compare allowing you to add or reduce the emphasis on certain metrics. It's important for these weights to total 1.0 otherwise you may find the resulting ranking doens't make sense.

As an example, the following would try to minimise the difference in player's level and try to ensure they're not the same class, this is done with a 0.7-0.3 weighting.
```csharp
MatchmakerRankingBuilder builder = new MatchmakerRankingBuilder();

builder.MinimiseDifference(entity1.Level, entity2.Level, 100, 0.7);
builder.NotEqual(entity1.Class, entity2.Class, 0.3);

return builder.Ranking;
```
