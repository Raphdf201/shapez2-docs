# Custom side upgrades

You can make custom side upgrades (things you buy with research points) and use them as a way to unlock your buildings.

```csharp
var sideUpgradeBuilder = SideUpgrade.new()
    .WithPresentationData(new SideUpgradePresentationData(
        new ResearchUpgradeId("mySideUpgradeId"),
        null,// Preview image
        null,// Preview video
        "Upgrade title".T(),
        "Upgrade description".T(),
        false,// Hidden
        "Buildings"))// Category
    .WithCost(new ResearchCostPoints(new ResearchPointCurrency(50)).AsEnumerable())
    .WithoutCustomRequirements();

AtomicBuildings.Extend()
    ...
    .UnlockedWithExistingSideUpgrade(new CustomSideUpgradeSelector(sideUpgradeBuilder))
    ...
```
