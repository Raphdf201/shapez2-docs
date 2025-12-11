# Console Commands Flow API

## Overview

The Console Commands Flow API provides a simple, fluent interface for registering custom console commands in your mods without needing to create custom rewirer classes.

## Quick Start

### Using Extension Methods

```csharp
using ShapezShifter.Flow;
using ShapezShifter.Hijack;

public class Main : IMod
{
    public Main()
    {
        this.RegisterConsoleCommand("mymod.hello", context =>
        {
            context.Output("Hello World!");
        });
        this.RegisterCheatCommand("mymod.cheat", context =>
        {
            context.Output("Stop cheating !");
        });
    }
}
```

## Working with Command Context

The `DebugConsole.CommandContext` provides:

- `context.Options` - Array of arguments passed to the command
- `context.Output(string)` - Output text to the console

### Example: Echo Command (WIP)

```csharp
this.RegisterConsoleCommand("mymod.echo", context =>
{
    if (context.Options.Length == 0)
    {
        context.Output("Usage: mymod.echo <message>");
        return;
    }

    string message = string.Join(" ", context.Options);
    context.Output($"Echo: {message}");
});
```

### Example: Command with Multiple Arguments (WIP)

```csharp
this.RegisterConsoleCommand("mymod.add", context =>
{
    if (context.Options.Length != 2)
    {
        context.Output("Usage: mymod.add <num1> <num2>");
        return;
    }

    if (!int.TryParse(context.Options[0], out int num1) || 
        !int.TryParse(context.Options[1], out int num2))
    {
        context.Output("Error: Invalid numbers");
        return;
    }

    int result = num1 + num2;
    context.Output($"{num1} + {num2} = {result}");
});
```

## Best Practices

### Command Naming

- Use a namespace prefix: `mymod.commandname`
- Use lowercase and dot notation
- Be descriptive: `mymod.spawn.enemy` vs `mymod.se`

### Error Handling (WIP)

Always validate input and provide helpful error messages:

```csharp
this.RegisterConsoleCommand("mymod.teleport", context =>
{
    if (context.Options.Length != 2)
    {
        context.Output("Usage: mymod.teleport <x> <y>");
        context.Output("Example: mymod.teleport 100 200");
        return;
    }

    if (!float.TryParse(context.Options[0], out float x) || 
        !float.TryParse(context.Options[1], out float y))
    {
        context.Output("Error: Coordinates must be numbers");
        return;
    }

    // Teleport logic here
    context.Output($"Teleported to ({x}, {y})");
});
```

### Removable commands

If you need your command to dynamically appear or disappear, you can register and unregister it like this :

```csharp
private RewirerHandle _handle;

public Main()
{
    _handle = this.RegisterConsoleCommand("mymod.cmd", context =>
    {
        context.Output("temp command");
    });
}

public void Dispose()
{
    GameRewirers.RemoveRewirer(_handle);
}
```

### One-Time Commands

If you don't need to remove a command, you can skip storing the handle:

```csharp
public Main()
{
    // Fire-and-forget registration
    this.RegisterConsoleCommand("mymod.version", context =>
    {
        context.Output("MyMod v1.0.0");
    });
}
```
