# Custom save data

## Step 1: Define Your Data Class

```csharp
[Serializable]
public class MyModData
{
    public int MyCounter { get; set; }
    public string MyText { get; set; }
}
```

## Step 2: Create Save Data Handler

```csharp
public class MyMod : IMod
{
    private ModSaveData<MyModData> _saveData;

    public MyMod()
    {
        // Option A: Manual filename
        _saveData = new ModSaveData<MyModData>("my-mod-data.json");
        
        // Option B: Automatic filename from assembly
        _saveData = this.CreateSaveData<MyModData>();
    }
}
```

## Step 3: Use Your Data

```csharp
// Read
int counter = _saveData.Data.MyCounter;

// Write
_saveData.Data.MyCounter++;
_saveData.Data.MyText = "Hello World";

// Reset
_saveData.Reset();
```

That's it! The data automatically saves and loads.

## Optional: Listen to Events

```csharp
_saveData.OnDataLoaded += data =>
{
    Console.WriteLine($"Loaded: {data.MyCounter}");
};

_saveData.OnDataSaving += data =>
{
    Console.WriteLine($"Saving: {data.MyCounter}");
};
```
