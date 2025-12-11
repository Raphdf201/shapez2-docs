# Setup modding environment

First, setup the shapez shifter API ([official instructions](https://tobspr-games.notion.site/shapez2-modding-documentation#2583c9e752e080728e40eae35a163fce)).
Most of the features documented on this site use my own fork of the shapez shifter API. To use it, simply
[clone](https://github.com/Raphdf201/shapez2-shifter) it, build it, and then set the SPZ2_SHIFTER variable to
`%SPZ2_PERSISTENT%\mods\ShapezShifter\ShapezShifter.dll` on windows, or `$SPZ2_PERSISTENT/mods/ShapezShifter/ShapezShifter.dll`.
If you use my fork, make sure to unsubscribe from the official one on steam workshop or it may cause problems.
Then, in your mod's manifest, add a shapez shifter dependency

```json
{
    "ModId": "local:ShapezShifter",
    "ModTitle": "Shapez Shifter",
    "Version": "0.9.1"
}
```
