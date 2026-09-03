# Proton WARDOGS – Elytra compatibility build

Experimental Proton/Wine compatibility work for **WARDOGS Playtest**.

> [!WARNING]
> **Multiplayer is not currently working reliably.** The patched runtime can
> launch the game, enter the firing range, and connect to a server, but testing
> on September 3, 2026 ended in a server kick with `no valid heartbeat within
> window`. This strongly indicates that Elytra's runtime heartbeat is not being
> validated under Proton. Treat this release as an investigation build, not a
> multiplayer-ready fix.

- Steam App ID: `4809930`
- Initially developed against Build ID: `25078803`
- Also exercised against Build ID: `25098628`
- Base runtime: Proton Experimental 11.0 (`experimental-11.0-20260826-x86_64`)

## Status

The patched runtime has launched WARDOGS through the Elytra launcher, reached
the playable firing range on Linux, and connected to a multiplayer server.
However, the server subsequently kicked the client because no valid anti-cheat
heartbeat arrived within the required window. Module installation and initial
launch compatibility are therefore improved, but end-to-end Elytra multiplayer
compatibility has **not** been achieved.

This is compatibility work, **not an anti-cheat bypass**. It does not patch Elytra, modify its downloaded modules, suppress failures, spoof successful checks, or disable validation. The changes implement missing Wine behavior and return real failures from Wine's driver and cryptography paths.

## Install the binary release

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
- overlap-safe in-place symmetric decryption

Source branch and commit:

- https://github.com/AstralDrift/wine/tree/wardogs-elytra-proton-compat
- https://github.com/AstralDrift/wine/commit/86e5df2

## Important

This is an unofficial test build. WARDOGS currently advertises Windows support, and its developers can change Elytra or the game build at any time. Do not use DLL overrides, unsigned replacement modules, launcher bypasses, or modified anti-cheat files.
