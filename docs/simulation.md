# Simulations

The simulation is the thing that calculates what your building should output based on its inputs.
It decides what signal is returned with logic gates, or what part of the shape disappears in a cutter.

## General

A simulation needs 3 things, the simulation itself, the simulation factory and the simulation renderer.
The simulation itself is responsible for the calculation. The factory converts a state (inputs) to a simulation result (outputs).
The renderer can change your building's appearance depending on what is going on inside.

## Belt simulations

TODO

## Wire simulations

### 2 in 1 out simulation

If your wire building takes 2 inputs and 1 output, your simulation class can extend `LogicGate2In1OutSimulationState`.
A `LogicGate2In1OutSimulationState` only has 1 mandatory method, which is `ComputeOutputSignal` where you get signal `a`
and `b`, and you just need to output the result.

```csharp
// AND gate example
protected override ISignal ComputeOutputSignal(ISignal a, ISignal b)
{
    return IntegerSignal.Get(a.IsTruthy() && b.IsTruthy());
}
```

### Custom simulation

To make a custom simulation, you need to make a simulation and a simulation state class. The state class needs to implement
`ISimulationState`. The simulation class needs to extend `Simulation<YourSimulationStateClass>` and implement
`IUpdatableSimulation`, along with `IItemSimulation` and/or `ISignalSimulation` and/or `IFluidSimulation`.

### Signal types

The possible signal types are

- BeltItemSignal (shape)
- ConflictSignal (conflict)
- FluidSignal (color)
- IntegerSignal (number)
- NullSignal (no signal)

All signals extend the `ISignal` interface. That means you can make a custom signal type by implementing the interface.

## Fluid simulations

TODO
