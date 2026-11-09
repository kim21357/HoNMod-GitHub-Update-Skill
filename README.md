# GitHub Auto Updates for Heroes of Newerth (.honmod)

**A step-by-step guide for HoN mod developers using Newerth Forge 1.8.1 and later.**

Newerth Forge lets players check for new versions of your `.honmod` through GitHub, download the updated package, and then apply it to Heroes of Newerth. You manage releases in **your own public repository**. Players choose when to check and update; it is **not** an automatic download or game-side Apply.

> **Already installed mods:** To discover GitHub updates, the *older installed* `.honmod` must already include the GitHub update attributes. If it does not, players must manually install a GitHub-enabled version once.

## Quick start

1. Create or choose a **public** GitHub repository.
2. Add `updaterepo` and `updateid` to the root `<modification>` element in your `mod.xml`.
3. Create `mods.json` at the **root of your repository's default branch** (usually `main`).
4. Package your updated `.honmod`, publish it as an asset in a normal **GitHub Release** (not a draft or prerelease), and calculate its SHA-256.
5. Update `mods.json` **after** the new Release is available.
6. Test from an *older GitHub-enabled* mod using **Custom Mod (.honmod) → CHECK UPDATES → UPDATE**, then **Apply Mods** if game changes need applying.

**[Full developer skill and troubleshooting → SKILL.md](SKILL.md)**

## 1. Add update information to `mod.xml`

Keep your existing mod actions and metadata. Add the two update attributes to the opening `<modification>` element:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<modification
    application="Heroes of Newerth Reborn"
    appversion="*"
    mmversion="*"
    name="Example Mod"
    author="Your Name"
    version="1.0.0"
    updaterepo="YourName/YourModRepository"
    updateid="example-mod"
>
    <!-- Keep your real Mod operations here -->
</modification>
```

| Attribute | Meaning |
| --- | --- |
| `version` | Version packaged in this particular `.honmod` |
| `updaterepo` | **`OWNER/REPO`**, not the full GitHub URL |
| `updateid` | Permanent, lowercase Mod ID (letters, digits, hyphens) |

Use the **same Mod name, `updaterepo`, and `updateid`** across releases. Do not change `updateid` just because the version changed. You can also set these through supported Newerth Forge Mod Editor / Converter fields.

## 2. Create `mods.json` in your repository

Your *mod developer's* repository might look like this:

```text
YourModRepository/
├── README.md
└── mods.json
```

Example contents **for the new version 1.1.0**:

```json
{
  "schemaVersion": 1,
  "mods": {
    "example-mod": {
      "name": "Example Mod",
      "version": "1.1.0",
      "releaseTag": "example-mod-v1.1.0",
      "asset": "Example-Mod-v1.1.0.honmod",
      "sha256": "REPLACE_WITH_ACTUAL_64_CHARACTER_LOWERCASE_SHA256",
      "notes": "Bug fixes and improvements"
    }
  }
}
```

This example hash **is not valid**. Replace it before using this index. The `example-mod` key must exactly match `updateid`. The `releaseTag` and `asset` must match the **published** GitHub Release tag and exact uploaded `.honmod` filename. You may also add an optional `"size": 12345` field containing the real file size **in bytes**.

A single `mods.json` can contain multiple Mods under different IDs. Each Mod/version can have its own GitHub Release tag; do **not** rely on the repository-wide "Latest release" to select the correct package.

## 3. Calculate the file's SHA-256

Finish packaging your `.honmod` **before** hashing it. In Windows PowerShell:

```powershell
(Get-FileHash -Algorithm SHA256 ".\Example-Mod-v1.1.0.honmod").Hash.ToLowerInvariant()
(Get-Item ".\Example-Mod-v1.1.0.honmod").Length
```

Copy the actual hash into `sha256` and, if you include `size`, copy the file size. Recalculate both if the archive changes. A `.honmod` must be a ZIP-compatible archive with `mod.xml` at its root.

## 4. Publish a GitHub Release

In your Mod repository, open **Releases → Create a new release**:

1. Set the tag to `example-mod-v1.1.0`.
2. Attach the final **`Example-Mod-v1.1.0.honmod`** file under Release Assets (not a source-code ZIP).
3. Publish it as a **normal release**, not Draft or Pre-release.
4. Only **after the file is published**, update your default-branch `mods.json` with the correct version, tag, asset, hash, and optional size.

Publishing the asset first prevents players from seeing an update that they cannot download.

## 5. What players see in Newerth Forge

1. Open **Custom Mod (.honmod)**.
2. Click **CHECK UPDATES**.
3. If the version in `mods.json` is higher than the installed version, choose **UPDATE**.
4. Newerth Forge downloads the exact GitHub Release asset, checks its hash and package identity/version, and backs up the old archive before replacing it.
5. Use **Apply Mods** separately to apply the new Mod to the game while the game is closed.

**CHECK UPDATES does not automatically install anything.** UPDATE requires a user click; downloading the archive does not automatically Apply it to HoN. Repository lookups may be cached for approximately 15 minutes.

## 6. Example: next release

| Field | Old package | New package |
| --- | --- | --- |
| `version` | `1.1.0` | `1.2.0` |
| `releaseTag` | `example-mod-v1.1.0` | `example-mod-v1.2.0` |
| `asset` | `Example-Mod-v1.1.0.honmod` | `Example-Mod-v1.2.0.honmod` |
| `sha256` | Previous file's checksum | New file's checksum |
| `updateid` | `example-mod` | **Unchanged** |
| `updaterepo` | `YourName/YourModRepository` | **Unchanged** |

Update the version inside the new archive's `mod.xml` too. Never publish a higher `mods.json` version before the matching Release and asset are available.

## 7. Requirements and troubleshooting

- Public GitHub repositories only; do not store passwords, personal access tokens, or API secrets in Mod files.
- Maximum download size: **64 MiB**; maximum archive entries: **5,000**; advertised extracted size: **256 MiB**.
- Use straightforward numeric versions such as `1.0.0`, `1.0.1`, `1.1.0`. The comparison is **not** full prerelease SemVer ordering.
- **No update found?** Check whether the installed *older* package includes both GitHub attributes, and whether `mods[updateid].version` is higher.
- **404?** Check that `mods.json` is at the default-branch root and the exact release tag and asset exist.
- **403 / 429?** GitHub may be rate-limiting requests.
- **Hash mismatch?** Recalculate the checksum from the final uploaded archive. Do not bypass validation.
- **Package mismatch?** Keep the Mod name, repository, ID, and version consistent.
- **Updated but not visible in game?** Check whether **Apply Mods** is still required.

SHA-256 helps verify the download matches the published index, but does not guarantee the Mod publisher is trustworthy.

## Reference files

| File | Purpose |
| --- | --- |
| **[SKILL.md](SKILL.md)** | Full reusable developer workflow and troubleshooting |
| [mod.example.xml](mod.example.xml) | Generic `mod.xml` metadata example |
| [mods.example.json](mods.example.json) | Generic update index example |
| [IMPLEMENTATION.md](IMPLEMENTATION.md) | Implementation details, limits, and compatibility notes |

### Working test case

[NF In-Game Matchmaking test documentation](https://github.com/kim21357/NF-In-Game-Matchmaking/blob/main/NF-In-Game-Matchmaking-Test/README.md) provides an example of a GitHub-hosted Mod update test. **You do not need to use that repository or its IDs.** Replace all placeholders with your *own* Mod name, public repository, version, file, SHA-256, and update ID.

**This repository is a guide and example collection, not a shared update host for other authors' Mods.**

[Newerth Forge Releases](https://github.com/KRAT0Sz/mod-launcher/releases)
