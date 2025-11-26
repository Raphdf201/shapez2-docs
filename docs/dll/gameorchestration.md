# Game.Orchestration.dll

## Game.Orchestration.BuiltinSimulationSystems.cs

This class contains all the initialization for the simulation systems. To add your building's simulation to the game,
you need to hook into the corresponding method. For example, for adding a wire simulation :

```csharp
private readonly Hook _modSystemHook;

public Main()
{
    // ...
    Hook modSystemsHook = DetourHelper
        .CreatePostfixHook<BuiltinSimulationSystems, IEnumerable<ISimulationSystem>>(
            simulationSystems => simulationSystems.CreateSimulationSystems(),
            CreateModSystems);
    // ...
}

// This method will be executed at the end of the the game's CreateSimulationSystems() method
private IEnumerable<ISimulationSystem> CreateModSystems(
    BuiltinSimulationSystems builtinSimulationSystems,
    IEnumerable<ISimulationSystem> systems)
{
    return systems.Append(new AtomicStatefulBuildingSimulationSystem<NAndGateSimulation, LogicGate2In1OutSimulationState>(
        new NAndGateSimulationFactory(), _defId));
}
```
