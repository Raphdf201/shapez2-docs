# Console Commands Flow API

## Overview

The Console Commands Flow API provides a simple, fluent interface for registering custom console commands in your mods without needing to create custom rewirer classes.

## Quick Start

A command can be registered using the RegisterConsoleCommand method. It has the following signature.

```csharp
public static RewirerHandle RegisterConsoleCommand(this IMod mod, string commandName,
            Action<DebugConsole.CommandContext> handler, bool isCheat = false, DebugConsole.ConsoleOption? arg1 = null,
            DebugConsole.ConsoleOption? arg2 = null)
```

```csharp
using ShapezShifter.Flow;
using ShapezShifter.Hijack;

public class Main : IMod
{
    public Main()
    {
        //...
        this.RegisterConsoleCommand("hello", context =>
        {
            context.Output("Hello World!");
        });
        this.RegisterConsoleCommand("cheat", context =>
        {
            context.Output("Stop cheating !");
        }, true);// true means cheat here
        //...
    }
}
```

## Working with Command Context

The `DebugConsole.CommandContext` provides:

- `context.Options` - Array of arguments passed to the command
- `context.Output(string)` - Output text to the console

A `DebugConsole.ConsoleOption` can be a string, bool, int, float or long.
To get the value of an argument, you can use `context.get...(n)` where ... is the option type and n is the argument position.
You can also use the Get<>(n) method to cast the argument to any type.

### Example: Echo Command

```csharp
this.RegisterConsoleCommand("echo", context =>// the command ingame will be modname.echo
    {
        if (context.Options.Length == 1)// there is one argument
        {
            context.Output($"Echo: {context.GetString(0)}");// 0 is the first argument
        }
        else
        {
            context.Output("Usage: mymod.echo <message>");
        }
    }, arg1: new DebugConsole.StringOption("message"));
```

### Example: Command with Multiple Arguments

```csharp
this.RegisterConsoleCommand("add", context =>
{
    if (context.Options.Length != 2)
    {
        context.Output("Usage: mymod.add <num1> <num2>");
        return;
    }

    int num1 = context.getInt(0);
    int num2 = context.getInt(1);
    int result = num1 + num2;
    context.Output($"{num1} + {num2} = {result}");
});
```

## Best Practices

### Error Handling

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
    _handle = this.RegisterConsoleCommand("tempCommand", context =>
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
    this.RegisterConsoleCommand("version", context =>
    {
        context.Output("MyMod v1.0.0");
    });
}
```
