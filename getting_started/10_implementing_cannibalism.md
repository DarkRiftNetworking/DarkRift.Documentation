# Implementing Cannibalism
Since we’ve built the eating system generic it’s easy to let other players get eaten. Add the following just **after the food eating foreach loop**:
```csharp
foreach (Player p in players.Values)
{
    if (p != player && Math.Pow(player.X - p.X, 2) + Math.Pow(player.Y - p.Y, 2) < Math.Pow(player.Radius, 2))
    {
        player.Radius += p.Radius;
        SendRadiusUpdate(player);
        Kill(p);
    }
}
```
I’ll let you implement Kill how you see fit, it’s probably best to **send** a ‘killed’ **message** to the player so that they can show a **game over** screen.

And that’s it! Our game is complete. If you want to continue, here are some suggestions for extensions or alternatively you can go onto the advanced topics if you want more DarkRift!
- Full GUI
- Restart game once killed
- Animate the players (e.g. expand to engulf food, grow in size slowly when eating, etc.)
- Reduce speed as size increases
- Ability to shed weight to get a speed boost
- Pickups (e.g. invulnerability, speed boost, size boost, etc.)
