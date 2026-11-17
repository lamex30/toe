# toe

A friendly package manager for Gentoo, on top of `emerge`.

```
toe install firefox
toe remove firefox
toe search "pdf viewer"
toe update
```

- **Finds packages by loose names.** `mangowindowmanager`, `mango-wm` and `Mango WM` all find `gui-wm/mangowm`; `vscode`, `rg` and `nvim` work too. An inexact match always asks first.
- **Finds packages in overlays too.** When a package isn't in the enabled repositories, toe looks in [GURU](https://wiki.gentoo.org/wiki/Project:GURU) (and any overlay you add to `search_overlays` in `/etc/toe/toe.conf`), offers to enable that overlay, syncs it, unmasks `~amd64` with your yes, and installs: `toe install mangowm` is all it takes. `toe undo` disables the overlay again. `toe search` lists overlay packages too.
- **Says what downloads and what compiles.** When a package has a prebuilt `-bin` version (`firefox-bin`, `rust-bin`, …), toe asks which one you want and tells you whether the regular one would compile from source. Packages that compile get a warning before you say yes.
- **Sorts out `/etc/portage` for you.** Keywords (`~amd64`), USE flags and licenses that emerge needs are shown once, written to toe's own `zz-toe-*` files on yes, and `toe undo` reverts them.
- **Handles 32-bit dependencies.** Steam, Wine and friends need `abi_x86_32` on dozens of libraries. emerge names them one per run (and sometimes suggests turning the flag back off); toe walks the dependency tree and writes the whole list at once. `toe install steam` gets `games-util/steam-launcher`, not the `dev-python/steam` library.
- **Removes safely.** `toe remove` uses `--deselect` and `--depclean`, never `--unmerge`, and won't remove core system packages.
- **Shows real progress.** Each package gets an OpenRC-style `[ ok ]` line and a progress bar with the ebuild phase and the `[n/m]` count read from its build log.
- Speaks English or Russian, following the system's language (`TOE_LANG=en|ru` to pick one). In a Linux console (TTY) it uses only the characters the console font has.

## Install

toe is a single Python file and needs only Portage's Python module, which every Gentoo has.

```sh
sudo wget -O /usr/local/bin/toe https://raw.githubusercontent.com/lamex30/toe/main/toe
sudo chmod 755 /usr/local/bin/toe
sudo toe setup
```

`toe setup` adds a post-sync hook that keeps the search index fresh, the config file `/etc/toe/toe.conf`, and bash and fish completions.

Systems installed with [lamex30/gentooinstall](https://github.com/lamex30/gentooinstall) already have it.

## Update

```sh
sudo toe self-update
```

It downloads the latest `toe` from this repository, checks that it is a working toe, and replaces itself.

## Commands

| command | what it does |
|---|---|
| `toe install NAME…` (`i`) | find, choose binary or source, sort out keywords/USE/licenses, install |
| `toe remove NAME…` (`rm`) | looks only among installed packages; `--deselect` + `--depclean` |
| `toe search QUERY` (`s`) | by name and description |
| `toe info NAME` | versions, slots, branches, license |
| `toe update` (`up`) | sync, then update `@world`, then preserved-rebuild |
| `toe clean` | unneeded packages, old distfiles and binary packages |
| `toe list [-a]` | your packages (`@world`), or all |
| `toe why` / `toe files NAME`, `toe owns FILE` | what needs it, its files, who owns a file |
| `toe history [NAME]` | install history (qlop) |
| `toe doctor` | checks the system |
| `toe undo` | reverts toe's last changes in `/etc/portage` |
| `toe alias add NAME CAT/PKG` | your own name for a package |
| `toe self-update` | updates toe itself |

Everywhere: `-y` (no questions), `-n` (dry run), `--explain` (print the emerge commands), `-v` (raw emerge output). For `install`: `--bin` (always the prebuilt version), `--source`, `--reinstall`, `--oneshot`.

## License

MIT
