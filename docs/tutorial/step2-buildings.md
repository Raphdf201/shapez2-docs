# Buildings

## 2.1 : The building group

The building group will hold variants of your building, such as the flipped version.
A basic building group needs the title, description and icon. Here is a basic example from my nand gate mod :

```csharp
IBuildingGroupBuilder bldingGroup = BuildingGroup.Create(new("nandgroup"))
    .WithTitle("building-variant.nand-gate.title".T())
    .WithDescription("building-variant.nand-gate.description".T())
    .WithIcon(FileTextureLoader.LoadTextureAsSprite(res.SubPath("icon.png"), out _))// res is the mod resource locator. If you used my project generator, it should already be there
    .AsNonTransportableBuilding()// if the building transports items/fluids, use AsTransportableBuilding()
    .WithPreferredPlacement(DefaultPreferredPlacementMode.Single)
    .AutoConnected();
```

## 2.2 : The building connector data

The connector data is the layout of the connectors on your building. Multi-tile buildings are not currently supported. Basic wire example :

```csharp
IBuildingConnectorData connectorData = BuildingConnectors.SingleTile()
    .AddWireInput(WireConnectorConfig.CustomInput(TileDirection.North))// top
    .AddWireInput(WireConnectorConfig.CustomInput(TileDirection.South))// bottom
    .AddWireOutput(WireConnectorConfig.CustomOutput(TileDirection.East))// right
    .Build();
```

Using fluids or shapes is similar. Simply use Add(Fluid/Shape)(Input/Output) along with (Shape/Fluid)ConnectorConfig

## 2.3 : The simulation

The simulation is what processes the inputs and outputs of the building. It needs to extend the `Game.Core.Simulation.Simulation` class. There are already pre-made simulation classes that are easier to use. The following examples are all in the `Game.Content` dll. I omitted some simulations, because they were not relevant. You can see the full list by decompiling the `Game.Content` dll.

- BeltFilterSimulation
- BeltReaderSimulation
- ButtonSimulation
- ConstantSignalSimulation
- ConveyorSimulation
- DisplaySimulation
- FullCutterSimulation
- HalfCutterSimulation
- HalvesSwapperSimulation
- ItemProducerSimulation
- LabelSimulation
- LogicGate2In1OutSimulation // What i use for my nand gate
- LogicGateIfSimulation
- LogicGateNotSimulation
- MergerSimulation
- MixerSimulation
- PainterSimulation
- PipeGateSimulation
- StackerSimulation
- VirtualHalfCutterSimulation

Example NAND gate with `LogicGate2In1OutSimulation`

```csharp
public class NAndGateSimulation(LogicGate2In1OutSimulationState state) : LogicGate2In1OutSimulation(state)
{
    protected override ISignal ComputeOutputSignal(ISignal a, ISignal b)
    {
        return IntegerSignal.Get(!(a.IsTruthy() && b.IsTruthy()));
    }
}
```

Example bottom painter with `PainterSimulation`

```csharp
public class BottomPainterSimulation(PainterSimulationState state) : PainterSimulation(state)
{
    protected override IBeltItem PaintItem(ShapeDefinition shape, IShapeColor color)
    {
        for (var i = 0; i < shape.Layers[0].Parts.Length; i++)
        {
            shape.Layers[0].Parts[i].Color = color;
        }

        return new ShapeItem(shape);
    }
}
```

## 2.4 : Draw data

Draw data is the look of your building (what will be drawn to the screen).
First, make a class that extends `IBuildingCustomDrawData`. In that class, make a method that returns a `BuildingDrawData` object. Basic example :

```csharp
public static BuildingDrawData CreateDrawData()
{
    string baseMeshPath = Main.Res.SubPath("nand.fbx");
    Mesh baseMesh = FileMeshLoader.LoadSingleMeshFromFile(baseMeshPath);
    LOD6Mesh lodMesh = MeshLod.Create().AddLod0Mesh(baseMesh).BuildLod6Mesh();

    return new BuildingDrawData(
        renderVoidBelow: false,
        mainMeshPerLayer: [lodMesh, lodMesh, lodMesh],
        isolatedBlueprintMesh: lodMesh,
        combinedBlueprintMesh: lodMesh,
        previewMesh: lodMesh.LODClose,
        glassMesh: new LODEmptyMesh(),
        colliders: BoundingBoxHelper.CreateBasicCollider(baseMesh),
        customDrawData: new NAndGateDrawData(),
        hasCustomOverviewMesh: false,
        customOverviewMesh: null,
        simulationRendererDrawsMainMesh: false);
}
```

For more advanced things, check out the [rendering page](../rendering.md).

## 2.5 : The simulation renderer

There is two types of simulation renderer. Stateless and stateful. Stateless ones are always the same, and stateful ones depend on the state of the simulation.

### Stateless

```csharp
public class NAndGateSimulationRenderer(IMapModel map)
    : StatelessBuildingSimulationRenderer<NAndGateSimulation, IBuildingCustomDrawData>(map);
```

### Stateful

TODO

## 2.6 : The actual building

This process is very similar to making the building group. Simply call Building.Create() and fill all the fields. Basic example :

```csharp
IBuildingBuilder blding = Building.Create(new("nand"))
    .WithConnectorData(connectorData)
    .DynamicallyRendering<NAndGateSimulationRenderer, NAndGateSimulation, IBuildingCustomDrawData>(new NAndGateDrawData())
    .WithStaticDrawData(NAndGateDrawData.CreateDrawData())
    .WithoutPrediction()
    .WithoutSound()
    .WithoutSimulationConfiguration()
    .WithoutEfficiencyData();
```

## 2.7 : Registering the building

To register the building, use the AtomicBuildingExtender :

```csharp
AtomicBuildings.Extend()
    .AllScenarios()
    .WithBuilding(blding, bldingGroup)
    .UnlockedAtMilestone(new ByIndexMilestoneSelector(2))
    .WithDefaultPlacement()
    .InToolbar(ToolbarElementLocator.Root().ChildAt(2).ChildAt(6).ChildAt(^1).InsertAfter())
    .WithSimulation(new NAndGateFactoryBuilder())
    .WithCustomModules(new NAndGateModuleDataProvider())
    .Build();
```
