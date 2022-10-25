---
title: "Obsidiant setup for quartz"
---

In order for Obsidian to behave has Quartz expect, there is a few [settings to change](https://quartz.jzhao.xyz/notes/obsidian#settings).

Go in Settings > Files & Links:
- `New Link` should be `Absolute Path in Vault"
- `Automatically update internal links` should be `on`

Or check that the following properties exist in `content/.obsidian/app.json`:
```json {title="content/.obsidian/app.json"}
{
  "alwaysUpdateLinks": true,
  "newLinkFormat": "absolute"
}
```
