# Custom Keybindings API

The Keybindings API allows mods to add custom keybindings to Shapez 2 that integrate seamlessly with the game's input system.

## Overview

Keybindings in Shapez 2 are organized into **layers** (like "main", "camera", "toolbar"), and each layer contains multiple **keybindings**. Each keybinding has:

- A **unique ID** (composed of layer + partial ID, e.g., "main.my-custom-action")
- A **primary key** (keyboard or controller)
- An optional **secondary key** (alternative binding)
- Settings for UI blocking and axis thresholds

## Quick Start

### Basic Example

```csharp
using ShapezShifter.Hijack;
using UnityEngine;
using System.Collections.Generic;

public class MyKeybindingsRewirer : IKeybindingsRewirer
{
    public void ModifyKeybindingLayers(List<KeybindingsLayer> layers)
    {
        // Method 1: Using the fluent builder (recommended)
        layers.AddKeybinding()
            .InLayer("main")
            .WithId("my-custom-action")
            .WithPrimaryKey(KeyCode.K)
            .WithSecondaryKey(KeyCode.L)
            .Build();
    }
}

// Register in your mod's initialization
var rewirer = new MyKeybindingsRewirer();
GameRewirers.AddRewirer(rewirer);
```

### Using the Keybinding

In your game code (e.g., in a custom system or hijacker):

```csharp
public class MyCustomSystem
{
    private InputDownstreamContext inputContext;
    
    public void Update(InputDownstreamContext input)
    {
        inputContext = input;
        
        // Check if the keybinding was just pressed
        if (inputContext.ConsumeWasActivated("main.my-custom-action"))
        {
            // Your action here
            Debug.Log("Custom keybinding activated!");
        }
        
        // Or check if it's currently held down
        if (inputContext.IsActive("main.my-custom-action"))
        {
            Debug.Log("Custom keybinding is held down");
        }
    }
}
```

## API Reference

### IKeybindingsRewirer Interface

Implement this interface to add custom keybindings:

```csharp
public interface IKeybindingsRewirer : IRewirer
{
    void ModifyKeybindingLayers(List<KeybindingsLayer> layers);
}
```

### Fluent Builder API

The recommended way to create keybindings:

```csharp
layers.AddKeybinding()
    .InLayer("main")                    // Required: Layer ID
    .WithId("my-action")                // Required: Keybinding ID
    .WithPrimaryKey(KeyCode.K)          // Required: Primary key
    .WithSecondaryKey(KeyCode.L)        // Optional: Alternative key
    .BlockableByUI(true)                // Optional: Can UI block this? (default: true)
    .WithAxisThreshold(0.5f)            // Optional: For analog inputs (default: 0.001)
    .Build();
```

#### Builder Methods

- **InLayer(string layerId)** - Sets the layer where the keybinding will be added
  - Built-in layers: "global", "main", "camera", "toolbar", "building-placement", "mass-selection", "waypoints", "accessibility", "debug"
  - You can also create custom layers
  
- **WithId(string partialId)** - Sets the keybinding's partial ID
  - Full ID will be "layerId.partialId"
  
- **WithPrimaryKey(KeyCode key)** - Simple single key
- **WithPrimaryKey(KeyCode key, KeyCode modifier)** - Key with modifier (e.g., Ctrl+K)
- **WithPrimaryKey(KeyCode key, KeyCode mod1, KeyCode mod2)** - Key with two modifiers
- **WithPrimaryController(ControllerBinding button)** - Controller button

- **WithSecondaryKey(...)** - Same options as primary key, provides alternative binding
- **WithSecondaryController(ControllerBinding button)** - Controller alternative

- **BlockableByUI(bool blockable)** - Whether UI elements can block this keybinding (default: true)
- **WithAxisThreshold(float threshold)** - Threshold for analog inputs (default: 0.001)

### Helper Methods

For more advanced use cases, use `KeybindingsHelper`:

```csharp
// Find or create a layer
KeybindingsLayer layer = KeybindingsHelper.FindOrCreateLayer(layers, "my-custom-layer");

// Add a keybinding directly
KeybindingsHelper.AddKeybindingToLayer(
    layer,
    "my-action",
    new KeySet(KeyCode.K),
    new KeySet(KeyCode.L)
);

// Check if a keybinding exists
bool exists = KeybindingsHelper.KeybindingExists(layer, "my-action");

// Get platform-appropriate control key (Cmd on Mac, Ctrl elsewhere)
KeyCode ctrlKey = KeybindingsHelper.GetDefaultControlKey();

// Create pre-configured keybindings
Keybinding simple = KeybindingsHelper.CreateSimpleKeybinding("action", KeyCode.K);
Keybinding modified = KeybindingsHelper.CreateModifiedKeybinding("save", KeyCode.S, KeyCode.LeftControl);
Keybinding crossInput = KeybindingsHelper.CreateCrossInputKeybinding(
    "jump", 
    KeyCode.Space, 
    ControllerBinding.Action2
);
```

## Examples

### Example 1: Simple Keybinding

Add a simple key to the main layer:

```csharp
public class SimpleKeybindingRewirer : IKeybindingsRewirer
{
    public void ModifyKeybindingLayers(List<KeybindingsLayer> layers)
    {
        layers.AddKeybinding()
            .InLayer("main")
            .WithId("toggle-my-feature")
            .WithPrimaryKey(KeyCode.M)
            .Build();
    }
}
```

### Example 2: Keybinding with Modifier

Add Ctrl+Shift+D to the main layer:

```csharp
public class ModifiedKeybindingRewirer : IKeybindingsRewirer
{
    public void ModifyKeybindingLayers(List<KeybindingsLayer> layers)
    {
        layers.AddKeybinding()
            .InLayer("main")
            .WithId("debug-dump")
            .WithPrimaryKey(KeyCode.D, KeyCode.LeftControl, KeyCode.LeftShift)
            .Build();
    }
}
```

### Example 3: Cross-Platform Control Key

Use platform-appropriate control key (Cmd on Mac, Ctrl elsewhere):

```csharp
public class CrossPlatformKeybindingRewirer : IKeybindingsRewirer
{
    public void ModifyKeybindingLayers(List<KeybindingsLayer> layers)
    {
        KeyCode ctrlKey = KeybindingsHelper.GetDefaultControlKey();
        
        layers.AddKeybinding()
            .InLayer("main")
            .WithId("quick-save")
            .WithPrimaryKey(KeyCode.S, ctrlKey)
            .Build();
    }
}
```

### Example 4: Keyboard + Controller Support

Add a keybinding that works with both keyboard and controller:

```csharp
public class MultiInputKeybindingRewirer : IKeybindingsRewirer
{
    public void ModifyKeybindingLayers(List<KeybindingsLayer> layers)
    {
        layers.AddKeybinding()
            .InLayer("main")
            .WithId("special-ability")
            .WithPrimaryKey(KeyCode.Space)
            .WithSecondaryController(ControllerBinding.Action1)
            .Build();
    }
}
```

### Example 5: Custom Layer

Create a new layer for your mod's keybindings:

```csharp
public class CustomLayerRewirer : IKeybindingsRewirer
{
    public void ModifyKeybindingLayers(List<KeybindingsLayer> layers)
    {
        // All keybindings in this layer will have IDs like "mymod.action1", "mymod.action2"
        
        layers.AddKeybinding()
            .InLayer("mymod")
            .WithId("action1")
            .WithPrimaryKey(KeyCode.Alpha1)
            .Build();
            
        layers.AddKeybinding()
            .InLayer("mymod")
            .WithId("action2")
            .WithPrimaryKey(KeyCode.Alpha2)
            .Build();
    }
}
```

### Example 6: Non-Blockable Keybinding

Create a keybinding that works even when UI is open:

```csharp
public class NonBlockableKeybindingRewirer : IKeybindingsRewirer
{
    public void ModifyKeybindingLayers(List<KeybindingsLayer> layers)
    {
        layers.AddKeybinding()
            .InLayer("global")
            .WithId("emergency-pause")
            .WithPrimaryKey(KeyCode.Escape)
            .BlockableByUI(false)  // Works even when UI is focused
            .Build();
    }
}
```

### Example 7: Multiple Keybindings

Add multiple keybindings efficiently:

```csharp
public class MultipleKeybindingsRewirer : IKeybindingsRewirer
{
    public void ModifyKeybindingLayers(List<KeybindingsLayer> layers)
    {
        // Add multiple keybindings to the same layer
        string layerId = "mymod";
        
        layers.AddKeybinding()
            .InLayer(layerId)
            .WithId("build-mode")
            .WithPrimaryKey(KeyCode.B)
            .Build();
            
        layers.AddKeybinding()
            .InLayer(layerId)
            .WithId("destroy-mode")
            .WithPrimaryKey(KeyCode.X)
            .Build();
            
        layers.AddKeybinding()
            .InLayer(layerId)
            .WithId("copy-mode")
            .WithPrimaryKey(KeyCode.C)
            .Build();
    }
}
```

## Checking Keybinding State

Once you've registered keybindings, check their state using `InputDownstreamContext`:

```csharp
// Get the input context (usually passed to your system's Update method)
InputDownstreamContext input = gameInputManager.DownstreamContext;

// Check if just pressed this frame
if (input.ConsumeWasActivated("main.my-action"))
{
    // Triggered once when pressed
}

// Check if currently held down
if (input.IsActive("main.my-action"))
{
    // True while key is held
}

// Check if just released this frame
if (input.ConsumeWasDeactivated("main.my-action"))
{
    // Triggered once when released
}

// For analog inputs (like controller sticks)
float axisValue = input.ConsumeAsAxis("camera.move-up");
```

## Localization

To add localized names and descriptions for your keybindings, add translations to your mod's localization files:

```json
{
  "keybinding.mymod.my-action": "My Action",
  "keybinding.mymod.my-action.description": "Performs my custom action",
  "keybindings.mymod": "My Mod Keybindings"
}
```

The game will automatically use these translations in the keybindings menu.

## Best Practices

1. **Use unique layer names** - Prefix with your mod name (e.g., "mymod") to avoid conflicts
2. **Choose appropriate layers** - Use existing layers when your keybind fits their context
3. **Set BlockableByUI correctly** - Most keybindings should be blockable by UI
4. **Provide alternatives** - Use secondary keys for flexibility
5. **Support controllers** - Add controller bindings when appropriate
6. **Use platform-appropriate modifiers** - Use `GetDefaultControlKey()` for Ctrl/Cmd
7. **Add localization** - Always add translations for your keybindings

## Common Key Codes

```csharp
// Letters
KeyCode.A through KeyCode.Z

// Numbers
KeyCode.Alpha0 through KeyCode.Alpha9

// Function keys
KeyCode.F1 through KeyCode.F12

// Modifiers
KeyCode.LeftControl, KeyCode.RightControl
KeyCode.LeftShift, KeyCode.RightShift
KeyCode.LeftAlt, KeyCode.RightAlt
KeyCode.LeftMeta, KeyCode.RightMeta  // Cmd/Win key

// Mouse
KeyCode.Mouse0 (left), KeyCode.Mouse1 (right), KeyCode.Mouse2 (middle)

// Special
KeyCode.Space, KeyCode.Return, KeyCode.Escape
KeyCode.Tab, KeyCode.Backspace, KeyCode.Delete
```

## Controller Bindings

```csharp
ControllerBinding.Action1  // A/Cross
ControllerBinding.Action2  // B/Circle
ControllerBinding.Action3  // X/Square
ControllerBinding.Action4  // Y/Triangle
ControllerBinding.LeftBumper
ControllerBinding.RightBumper
ControllerBinding.LeftTrigger
ControllerBinding.RightTrigger
ControllerBinding.LeftStickUp/Down/Left/Right
ControllerBinding.RightStickUp/Down/Left/Right
ControllerBinding.DPadUp/Down/Left/Right
```

## Troubleshooting

**Keybinding not working:**

- Check that you've registered your rewirer with `GameRewirers.AddRewirer()`
- Verify the full keybinding ID (layer.partialId)
- Check if BlockableByUI is preventing activation when UI is open
- Ensure you're calling Consume methods, not just Is/Was methods

**Conflicts with existing keybindings:**

- Use a custom layer to isolate your keybindings
- Check existing keybindings in DefaultKeybindings.cs
- Consider making your keybinding optional via settings

**Not appearing in settings:**

- Add localization strings for your keybinding
- Restart the game after adding new keybindings
