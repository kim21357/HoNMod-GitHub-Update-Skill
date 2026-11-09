# GitHub Update Skill for Heroes of Newerth .honmod

A reusable, mod-independent developer guide for distributing and updating **.honmod** packages through public GitHub repositories and GitHub Releases using **Newerth Forge 1.8.1 Dev**.

## Start here

**[SKILL.md](SKILL.md)** — end-to-end setup, from `mod.xml` and `mods.json` through SHA-256, GitHub Releases, update checks, package verification, and troubleshooting.

| File | Purpose |
| --- | --- |
| [SKILL.md](SKILL.md) | Main reusable developer skill |
| [mod.example.xml](mod.example.xml) | Generic XML metadata example |
| [mods.example.json](mods.example.json) | Generic repository update index example |
| [IMPLEMENTATION.md](IMPLEMENTATION.md) | Technical implementation and compatibility notes |

### Minimal setup

Add the following **attributes** to your existing `<modification>` root element in `mod.xml`, preserving all of your mod's real edit/copy operations:

```xml
updaterepo="YOUR_ACCOUNT/YOUR_REPO"
updateid="your-mod-id"
```

Create `mods.json` in your repository's **default-branch root**, publish the final `.honmod` as a **GitHub Release asset**, and set its actual version, tag, asset filename, SHA-256 and size in the index.

The installed **older** package must include these update attributes to discover new versions. Checking updates never downloads or applies the mod; the user explicitly chooses UPDATE and later Apply Mods.

### Working example

[NF In-Game Matchmaking (separate mod repository)](https://github.com/kim21357/NF-In-Game-Matchmaking/blob/main/NF-In-Game-Matchmaking-Test/README.md) provides a real-world metadata-only update test. It is **not** required and is not the default repository for other mod authors.

This repository is only for documentation and example metadata. Do not upload mod-specific release assets here unless you deliberately want this to be their update repository.
