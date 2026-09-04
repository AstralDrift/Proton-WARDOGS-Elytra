# Proton WARDOGS – Elytra compatibility research build

Experimental Proton/Wine compatibility work for **WARDOGS Playtest**.

> [!WARNING]
> **This is not currently a playable multiplayer fix.** On Build `25078803`,
> the patched runtime reached the firing range and joined a server, but Elytra
> kicked the client with `no valid heartbeat within window`. On Build
> `25098628`, the authentic modules install and verify, but session preparation
> stops earlier: Elytra refuses the game executable while loading module
> `597ca429-3abf-43b2-aa78-73c8d7524be7` revision `43` (`0x8007001F`). Treat
> this release only as a reproducible Wine compatibility research build.

- Steam App ID: `4809930`
- Initially developed against Build ID: `25078803`
- Also exercised against Build ID: `25098628`
- Base runtime: Proton Experimental 11.0 (`experimental-11.0-20260826-x86_64`)

## Status

The patches fix several real Wine gaps found while tracing Elytra. They enabled
Build `25078803` to launch and reach a server, but did not produce a valid
runtime heartbeat. The current Build `25098628` still downloads, verifies, and
installs all three authentic Elytra modules, then fails during `session-prime`
while loading the Lighthouse module.

A native-Windows control run completed module installation, prepared the
session, and launched the same game successfully. The remaining Proton failure
is therefore local to Elytra's Windows-kernel-driver execution path. WARDOGS has
also stated that Proton support is not enabled for this playtest build while it
works on proper Proton emulation. A player-side Wine build cannot enable that
vendor-side support.

This is compatibility work, **not an anti-cheat bypass**. It does not patch Elytra, modify its downloaded modules, suppress failures, spoof successful checks, or disable validation. The changes implement missing Wine behavior and return real failures from Wine's driver and cryptography paths.

## Install the binary release (diagnostic testing only)

Do not install this expecting working multiplayer. It is retained so Wine,
Valve, and WARDOGS/Elytra developers can reproduce the compatibility progress
and the remaining failure without modifying any anti-cheat binary.

1. Download `Proton-WARDOGS-Elytra.tar.zst` from this repository's Releases page.
2. Exit Steam completely.
3. Create Steam's custom compatibility-tool directory if needed:

   ```bash
   mkdir -p ~/.local/share/Steam/compatibilitytools.d
   ```

4. Extract the archive there:

   ```bash
   tar --zstd -xf Proton-WARDOGS-Elytra.tar.zst \
     -C ~/.local/share/Steam/compatibilitytools.d
   ```

5. Start Steam, open **WARDOGS Playtest → Properties → Compatibility**, enable the forced compatibility tool, and select **Proton WARDOGS (Elytra compatibility)**.
6. Launch normally through Steam. Do not run Elytra or the game executable directly.

If an older failed prefix keeps producing errors, rename rather than delete it so it remains recoverable:

```bash
mv ~/.local/share/Steam/steamapps/compatdata/4809930 \
   ~/.local/share/Steam/steamapps/compatdata/4809930.backup
```

Steam will create a clean prefix on the next launch. Moving the prefix removes locally cached settings from the active location, but the backup preserves them.

## Implemented Wine fixes

- `RtlStringFromGUIDEx`
- real `NtLoadDriver` routing through Wine's SCM and `winedevice.exe`
- PE load-config security-cookie initialization for native drivers
- valid create-IRP security context and access state
- correct packed create disposition for device opens
- kernel `ksecdd.sys` BCrypt forwarding
- correct overlap-safe, padded in-place symmetric decryption
- generic access mapping for driver device opens
- `MmMapLockedPagesSpecifyCache`

Source branch, combined patch series, and commits:

- https://github.com/AstralDrift/wine/tree/wardogs-elytra-proton-compat
- `patches/` in this repository (applies to Valve Wine `proton_11.0`)
- https://github.com/AstralDrift/wine/commit/86e5df2
- https://github.com/AstralDrift/wine/commit/844889c
- https://github.com/AstralDrift/wine/commit/686ebe9

## Important

This is an unofficial test build. WARDOGS currently advertises Windows support,
and its developers can change Elytra or the game build at any time. Do not use
DLL overrides, unsigned replacement modules, launcher bypasses, modified
anti-cheat files, or any attempt to synthesize Elytra approval/heartbeat data.
