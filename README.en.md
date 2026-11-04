<p align="center">
  <a href="README.md"><img alt="한국어" src="https://img.shields.io/badge/%ED%95%9C%EA%B5%AD%EC%96%B4-%ED%95%9C%EA%B5%AD%EC%96%B4%EB%A1%9C%20%EB%B3%B4%EA%B8%B0-6e7681?style=for-the-badge"></a>
  <a href="README.en.md"><img alt="English (this page)" src="https://img.shields.io/badge/English-This%20page-1f6feb?style=for-the-badge"></a>
</p>

### ⚠️ IMPORTANT NOTICE / DISCLAIMER

- **Original Author:** swiftxp-hub (4.1 port reference: mattpsvreis)
- **Original Repository:** spt-show-me-the-money-quick-sell
- **Original Link:** https://github.com/swiftxp-hub/spt-show-me-the-money-quick-sell
- **License:** See upstream repository
- **This Port By:** R_F (danyhappy564-cmyk) — unofficial, AI-assisted port. Not affiliated with or endorsed by the original author.

1. **Reflection & Take-Downs:** I deeply reflect on the ECOT incident. As an AI-assisted "vibe coder," I will immediately delete files if the original authors ask.
2. **No Re-Distribution:** These ported builds are unverified, temporary fixes. Please do NOT re-upload or share them anywhere else.
3. **Do Not Pester Original Authors:** Never report bugs or pester original modders regarding issues from my unofficial ports.
4. **Full Credit & Respect:** I will always credit original creators on GitHub and prioritize their decisions above all else.
5. **Support Original Creators:** Instead of using my ports, please visit the original authors' Forge pages to leave kind words or tips.

---

## Changelog

The full changelog (1 entry) is in the [`changelog/`](changelog/) folder, 10 per file (higher number = newer, latest: [`CHANGELOG-001.md`](changelog/CHANGELOG-001.md)). Per-release summaries: [Releases](../../releases).

# Show Me The Money: Quick Sell — Fork changes

Original: [swiftxp-hub/spt-show-me-the-money-quick-sell](https://github.com/swiftxp-hub/spt-show-me-the-money-quick-sell)
(Show Me The Money addon for instantly selling items to traders/the flea market with a hotkey + mouse click)
4.1 port reference: [mattpsvreis/spt-show-me-the-money-quick-sell](https://github.com/mattpsvreis/spt-show-me-the-money-quick-sell)

This documents only changes made in this fork. Currently targets **SPT 4.1**.

---

## Build

**Build the main Show Me The Money mod first.** Its build copies itself into the
installation, and this project references that installed copy.

```
dotnet build
```

Paths come from `SptRoot` in `Directory.Build.props`. Priority:
`Directory.Build.props.user` → `-p:SptRoot=...` → environment variable `SPT_ROOT` → default
`E:\SPT 4.1`. `Directory.Build.targets` validates the path.

Override the main mod's DLL location separately with `ShowMeTheMoneyPluginPath`
(default `$(SptRoot)\BepInEx\plugins\com.swiftxp.spt.showmethemoney`).

Build output is copied directly to
`$(SptRoot)\BepInEx\plugins\com.swiftxp.spt.showmethemoney.quicksell\`.

---

<26/08/31 Detailed changes>

**Build** — Fixed two reasons fresh clones could not build.

- **Removed hardcoded game-assembly reference paths** — References pointed to
  `spt4-shared-dlls` three levels above the repository, a manually populated
  folder absent from fresh clones. Without it, every reference was unresolved.

  References now come from the SPT installation through `SPTPath`
  (default `E:\SPT 4.0.10`, overridable with `-p:SPTPath=...`).

- **Changed the main Show Me The Money reference** — Previously referenced
  the adjacent clone's `bin` folder, requiring a main-mod repository named exactly
  `spt-show-me-the-money` immediately beside this one, already built in Release.
  Other folder layouts could not satisfy this.

  Now references the installed plugin. Just build the main mod first;
  its build copies it into the installation.

- **Declared version (2.3.0)** — Undeclared versions made locally built DLLs report
  1.0.0. Set 2.3.0 to match the release corresponding to this source.

- Automatically copies the built plugin into the installation's plugins folder
  (skips absent paths, so builds still succeed elsewhere).

- Removed `Assets/icon.png` (repository size cleanup)

---

## Related fixes (in the main mod repository)

Two problems you may encounter with Quick Sell were fixed in the main mod's
[spt-show-me-the-money](https://github.com/danyhappy564-cmyk/spt-show-me-the-money)
fork. See its README for details.

- **All inventory clicks stopped responding** — The distributed Quick Sell calls
  `PluginContextDataHolder`, which has a different name in the main source tree.
  With a locally built main mod, Quick Sell's `GridItemView.OnClick` prefix
  fails to resolve and throws before the original `OnClick`. Item inspection
  and container opening both fail; removing Quick Sell fixes it, making it look like its bug.
  The main mod now forwards the old name.

- **Quick Sell refused to load because the main mod had no declared version** —
  `missing dependencies: com.swiftxp.spt.showmethemoney (v2.6.0 or newer)`.
  The main mod reported 1.0.0 because no version was declared.
  Fixed there by declaring 2.7.0.

- **Instant flea-sale settlement** is also in the main server mod
  (configured with `instant-flea-sell.json`).

---

<26/09/07 Detailed changes>

- **Ported to SPT 4.1.** Merged mattpsvreis's 4.1.2 port. This fork previously changed
  only build settings/README, so there were no code conflicts; all three conflicts were build settings/docs.

  - Build configuration — Adopted upstream's `SptRoot`/`SptManaged`/`SptBepInEx`
    layout and `Directory.Build.targets` validation, retaining this fork's `Version`
    declaration (2.3.0) and automatic installation copying. Added default `E:\SPT 4.1`
    so fresh clones build without `Directory.Build.props.user`. Also adopted upstream's
    new `0Harmony`/`UnityEngine.CoreModule` references

  - **Kept this fork's reference to the main Show Me The Money mod.** Upstream's 4.1 port
    reverted to an adjacent clone's `bin/Release/netstandard2.1/`, requiring
    a clone named exactly `spt-show-me-the-money` immediately beside this repository
    and already built in Release. Otherwise it silently stays unresolved. Referencing
    the installed plugin through `ShowMeTheMoneyPluginPath` also matches the main mod's
    self-installing build, so it was retained

- Kept the main mod's `PluginContextDataHolder` compatibility shim. Not needed when
  building this repository for 4.1 too, but retained for distributed Quick Sell 2.3.0 users.

