# Core.Localization.dll

## Core.Localization

### String.T()

This method is responsible for handling translations. The string you input is the translation id. The game will take care of
determining the correct translated string. Translated strings are located in a file called translations.json in the mod's
root folder. Translation strings can contain HTML. A simple translation file for only one language looks like this :

```json
{
  "en-US":
  {
    "building-variant.cutter-diagonal.title": "Diagonal Destroyer",
    "building-variant.cutter-diagonal.description": "<gl>Destroys</gl> the <gl>Even Parts</gl> of a shape."
  }
}
```

To access the translated strings above, do `translationId.T()`. For example, to access the diagonal cutter's title :

```csharp
"building-variant.cutter-diagonal.title".T()
```
