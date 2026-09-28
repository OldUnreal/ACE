# ACE

ACE (AntiCheatEngine) is an anti-cheat for Unreal Engine 1 games, mainly Unreal
Tournament, written by AnthraX. It runs on the game server. It checks every
connecting player's game, both the native code and the UnrealScript packages,
and kicks players whose game has been tampered with.

Current version: **v1.4e**, installed through **NPLoader v25**.

## Features

- Checks native code and UnrealScript for hooks, patches and injected code
- Uses a signed, automatically updated file list of known-good files
  (`ACEFileList.txt`)
- Optional AntiTweak protection, driven by `ACETweakList.txt`. It also detects
  brightskins.
- Both lists are maintained at <https://github.com/stijn-volckaert/ACE_Lists/>,
  and servers pick up updates without a restart.
- Takes screenshots and writes kick logs when a player is kicked. Admins can
  also take screenshots on demand.
- Collects hardware IDs and MAC hashes to identify players
- AutoConfig finds the mods your server runs (HUDs, HUD mutators, weapons,
  scoreboards, maps with embedded code) and adds them to the list of checked
  packages
- Optional, off by default: thread watchdog, memory reader detection, and code
  injection detection

## Platform support

| | Supported |
|---|---|
| **Servers** | UT v469 and newer (pre-469 dropped in v1.3h); Windows x86/x64, Linux x86/AMD64/ARM64 |
| **Clients** | Windows x86 and x64, Windows ARM64 via emulation, Linux x86/x64 (v469+), Wine/Proton |

## Quick install

The package uses the older folder layout, with one folder per architecture:
`System` (32-bit), `System64` (x86-64) and `SystemARM64` (Linux ARM64). How you
copy the files depends on your UT version:

- **UT 469f and newer.** All files live in a single `System` folder. Copy the
  contents of the package's `System` folder into your server's `System`
  folder. On a 64-bit server, also copy the contents of `System64` (x86-64) or
  `SystemARM64` (ARM64) into `System`, and replace any files with the same
  name.
- **UT 469e and older.** These versions use the same folder names as the
  package. Unzip the package into your server's UT folder as it is.

Then:

1. Make sure the `Logs` and `Shots` folders exist in your UT folder.
2. In `UnrealTournament.ini`, under `[Engine.GameEngine]`, add:
   ```ini
   ServerActors=NPLoader_v25.NPLActor
   ```
3. Restart the server. The rest of the installation happens automatically.

**Upgrading?** First remove every `ServerActors`/`ServerPackages` line that
refers to an older ACE or NPLoader version. Then delete the old ACE files
(`ACEv14d_*`, `NPLoader_ACEv14d.int`, …). If you copy settings over from an
older install, delete the `FileListProvider*`/`TweakListProvider*` settings.
[INSTALL.txt](INSTALL.txt) has the full steps.

## Configuration

The defaults work for most servers. The main settings live in
`[ACEv14e_S.ACEActor]`:

- **Custom mods that ACE doesn't recognize.** If a mod does its own rendering
  or input handling, list it in `UPackages` (up to 32 entries):
  ```ini
  [ACEv14_AutoConfig.ACEAutoConfigActor]
  UPackages[0]=MyMod.u
  ```
- **Firewall or fixed port.** ACE needs its own **UDP** port. By default it
  uses the game port + 2, or the next free port above that.
- **NAT.** ACE works out the server's WAN IP automatically. You can also set
  it yourself with `ForcedWANIP`.

## Documentation

| File | Contents |
|---|---|
| [INSTALL.txt](INSTALL.txt) | Clean install and upgrade steps, notes for mod authors |
| [SETTINGS.txt](SETTINGS.txt) | Full `[ACEv14e_S.ACEActor]` reference with defaults |
| [FIREWALL.txt](FIREWALL.txt) | PlayerManager UDP port setup |
| [NAT.txt](NAT.txt) | How to find or set the WAN IP behind a NAT router |
| [changes.txt](changes.txt) | Changelog for ACE and NPLoader |
| [LICENSE.md](LICENSE.md) | License terms |

## For mod authors

These packages are open source:

- **IACEv14**: interfaces for the ACE classes. **Do not recompile it**, because
  a rebuild changes the package GUID and breaks connecting to servers.
- **ACEv14e_EH**: the event handler, similar to UTDC's EventActor. You may edit
  it and share your changes.
- **ACEv14_AutoConfig**: fills in the `UPackages` list automatically. You may
  edit it.

A mod on the `UPackages` list can ship its own `ACEFileList-<ModName>.txt` and
`ACETweakList-<ModName>.txt` to add file hashes and tweak rules.

To get the source, export it from the `.u` files with UnrealEd or
wotgrealexporter, or download it from <https://github.com/stijn-volckaert/>.

## Support

<https://ut99.org/viewforum.php?f=66>

## License

ACE is freeware. You may redistribute it only as unmodified binaries. You may
build and distribute addons that use its API under any license. See
[LICENSE.md](LICENSE.md).
