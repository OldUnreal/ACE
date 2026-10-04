# ACE

ACE (AntiCheatEngine) is an anti-cheat for Unreal Engine 1 games, mainly Unreal
Tournament, written by AnthraX. It runs on the server and supported clients,
checks native code and UnrealScript packages, and kicks players when its
checks detect tampering.

ACE is installed through NPLoader. [changes.txt](changes.txt) lists the
release history of both, newest first.

## Features

- Checks native code and UnrealScript for hooks, patches and injected code
- Uses a signed, automatically updated file list of known-good files
  (`ACEFileList.txt`)
- Optional AntiTweak protection, driven by `ACETweakList.txt`, including
  checks for brightskin modifications
- Both lists are maintained at
  [ACE_Lists](https://github.com/stijn-volckaert/ACE_Lists/).
  Automatic updates are enabled by default. ACE checks for updates when it
  initializes for a map and applies downloaded lists without a server restart.
- Supports screenshots on kicks and on demand, plus server-side kick logs
- Reports hardware IDs and MAC hashes for player identification
- AutoConfig finds relevant server mods, including HUDs, HUD mutators,
  weapons, scoreboards and maps with embedded code, and adds them to the
  list of checked packages
- Optional (off by default): Thread watchdog, memory reader and code-injection detection

## Platform support

| Component | Supported |
|---|---|
| Servers | UT v469 and newer; Windows x86/x64, Linux x86/AMD64/ARM64 |
| Clients | Windows x86/x64, Windows ARM64 through emulation, Linux x86/x64 with UT v469+, Wine/Proton |

Support for pre-469 servers was dropped in ACE v1.3h.

## Quick install

The package contains `System` for 32-bit files, `System64` for x86-64, and
`SystemARM64` for Linux ARM64. Copy the files according to your UT version:

- **UT 469f and newer.** Copy the contents of the package's `System` folder
  into your server's `System` folder first. For a 64-bit server, then copy
  the contents of `System64` for x86-64 or `SystemARM64` for ARM64 into the
  same folder, replacing files with the same name. Copy `System` first,
  because the other folders don't contain every package.
- **UT 469 through 469e.** Unzip the package into your server's UT folder,
  keeping the package's folder layout.

Then:

1. Make sure the `Logs` and `Shots` folders exist in your UT folder.
2. In your server's ini file, under `[Engine.GameEngine]`, add:
   ```ini
   ServerActors=NPLoader_v26.NPLActor
   ```
   Use `UnrealTournament.ini`, or the file passed with `ini=` on the command line.
3. Restart the server. The rest of the installation happens automatically.

**Upgrading from an older ACE?** Before restarting, remove every
`ServerActors`/`ServerPackages` line that refers to an older ACE or NPLoader
version. Delete the old ACE files, such as `ACEv14d_*` and
`NPLoader_ACEv14d.int`.

If you copy settings from an older installation, remove
`FileListProviderHost`, `FileListProviderPath`, `TweakListProviderHost` and
`TweakListProviderPath` so ACE uses the current defaults.

[INSTALL.txt](INSTALL.txt) has the full installation and upgrade steps.

## Configuration

The defaults work for most servers. Server settings live in the installed
version's ACEActor section. AutoConfig has its own section.

The examples below use ACE v1.4e. Replace `v14e` with your installed revision.
The shared AutoConfig section remains `ACEv14_AutoConfig.ACEAutoConfigActor`
for ACE v1.4 releases.

If a custom mod does its own rendering or input handling and AutoConfig
doesn't recognize it, add it to AutoConfig's `UPackages` list:

```ini
[ACEv14_AutoConfig.ACEAutoConfigActor]
UPackages[0]=MyMod.u
```

AutoConfig accepts up to 255 entries, indexed from 0 to 254. With its
PackageHelper available, it rebuilds the ACEActor package list on every map
and filters entries against the server's package map. Custom mods normally
need to be listed in `ServerPackages`.

To maintain the ACEActor list manually, disable AutoConfig and use package
base names without `.u`. Keep entries consecutive, starting at index 0:

```ini
[ACEv14e_S.ACEActor]
bAutoConfig=false
UPackages[0]=MyMod
```

ACE needs a dedicated UDP port. By default it uses the game port + 2,
trying higher ports if that port is unavailable.

Behind a NAT router, ACE detects the server's public IP automatically.
To set it manually, disable autodetection and provide your public IPv4 address:

```ini
[ACEv14e_S.ACEActor]
bAutoFindWANIP=false
ForcedWANIP=203.0.113.10
```

Replace the example address with your server's actual public IPv4 address.

Spectator checking is controlled by `bCheckSpectators`. Set it to `true` if
you want ACE to check spectators as well.

See [SETTINGS.txt](SETTINGS.txt) for server and AutoConfig options,
[FIREWALL.txt](FIREWALL.txt) for network setup, and [NAT.txt](NAT.txt) for
WAN-IP configuration.

## Documentation

| File | Contents |
|---|---|
| [INSTALL.txt](INSTALL.txt) | Clean installation, upgrades and notes for mod authors |
| [SETTINGS.txt](SETTINGS.txt) | Common server and AutoConfig settings, examples and limitations |
| [FIREWALL.txt](FIREWALL.txt) | PlayerManager UDP port setup |
| [NAT.txt](NAT.txt) | Automatic, cached and manual WAN-IP configuration |
| [changes.txt](changes.txt) | Changelog for ACE and NPLoader |
| [LICENSE.md](LICENSE.md) | License terms |

## For mod authors

These packages are open source:

- **IACEv14** contains the interfaces for the ACE classes. **Do not recompile
  it**, because a rebuild changes its package GUID and breaks compatibility
  with servers using the original package.
- **ACEv14e_EH** is the event handler, similar to UTDC's EventActor. Its name
  follows the installed ACE revision. You may edit it and share your changes.
- **ACEv14_AutoConfig** fills in the `UPackages` list automatically. You may
  edit it.

A mod on the checked-package list can supply `ACEFileList-<ModName>.txt` and
`ACETweakList-<ModName>.txt` to add file hashes and tweak rules. With the default
list names, place these files alongside the corresponding main lists on the
server.

To get the source, export it from the `.u` files with UnrealEd or
wotgrealexporter, or download it from
[AnthraX's GitHub repositories](https://github.com/stijn-volckaert/).

## Support

Ask in the `#ace-discussion` channel on the
[OldUnreal Discord](https://discord.gg/thURucxzs6).

## License

ACE is freeware. You may redistribute it only as unmodified binaries. You may
build and distribute addons that use its API under any license. See
[LICENSE.md](LICENSE.md).
