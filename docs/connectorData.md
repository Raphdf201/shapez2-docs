# Connector configs

A Connector config or Connector data is the layout of your building's connectors (belts, wires and pipes).
For now, only single tile buildings are supported.

Examples :

```csharp
// Simple belt-only building where a belt enters from the left and exits to the right
var connectorData = BuildingConnectors.SingleTile()
    .AddShapeInput(ShapeConnectorConfig.DefaultInput())
    .AddShapeOutput(ShapeConnectorConfig.DefaultOutput())
    .Build();
```

```csharp
// Simple logic gate which has inputs on both sides and outputs on the top
var connectorData = BuildingConnectors.SingleTile()
    .AddWireInput(WireConnectorConfig.CustomInput(TileDirection.West))
    .AddWireInput(WireConnectorConfig.CustomInput(TileDirection.East))
    .AddWireOutput(WireConnectorConfig.CustomOutput(TileDirection.North))
    .Build();
```

```csharp
// Shape depainter
var connectorData = BuildingConnectors.SingleTile()
    .AddShapeInput(ShapeConnectorConfig.DefaultInput())
    .AddShapeOutput(ShapeConnectorConfig.DefaultOutput())
    .AddFluidOutput(FluidConnectorConfig.CustomOutput(TileDirection.North))
    .Build();
```
