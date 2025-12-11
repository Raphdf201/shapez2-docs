# Periodic functions

To run something periodically, simply call the RunPeriodically method on your IMod instance. The lambda will be ran at
each tick. The gameSessionOrchestrator can be use to check if keybindings are pressed, for example. The time variable is the
time elapsed since the last tick, in seconds. (not sure)

```csharp
this.RunPeriodically((gameSessionOrchestrator, time) =>
    {
        // do things
    });
```
