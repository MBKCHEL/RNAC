# 📦 Package Managers Cheat Sheet
**Legend**

- `pkg` = package name, `file` = path to a local package file
- `#` = comment, `$` is omitted for easier copying
- Commands that need root are written with `sudo` (on BSD use `doas` or `su` if there is no sudo)
- ⚠️ = a trap that is easy to fall into

---

## Table of contents

|Category|Managers|
|---|---|
|Arch family|[pacman](#pacman) · [yay](#yay) · [paru](#paru)|
|Debian family|[apt](#apt) · [dpkg](#dpkg)|
|Red Hat family|[dnf](#dnf) · [yum](#yum) · [rpm](#rpm)|
|SUSE|[zypper](#zypper)|
|Mandriva / Mageia|[urpmi](#urpmi)|
|PLD|[poldek](#poldek)|
|Void|[xbps](#xbps)|
|Gentoo|[emerge](#emerge)|
|Alpine|[apk](#apk)|
|Slackware family|[slackpkg](#slackpkg) · [slapt-get](#slapt-get) · [swaret](#swaret) · [netpkg](#netpkg)|
|Solus / Pardus|[eopkg](#eopkg) · [pisi](#pisi)|
|Source-based minimalist|[kiss](#kiss) · [cpt](#cpt)|
|Tiny Core|[tce](#tce)|
|Foresight|[conary](#conary)|
|Clear Linux|[swupd](#swupd)|
|BSD|[pkgsrc](#pkgsrc) · [ports](#ports)|
|Functional / declarative|[nix](#nix) · [guix](#guix)|
|Universal (sandboxed apps)|[flatpak](#flatpak) · [snap](#snap)|
|Language ecosystems|[cargo](#cargo) · [pip](#pip) · [npm](#npm) · [yarn](#yarn) · [pnpm](#pnpm) · [gem](#gem) · [composer](#composer)|
|Unclear|[upkg](#upkg)|

---

## Quick comparison (main system managers)

|Action|pacman|apt|dnf|zypper|apk|xbps|emerge|
|---|---|---|---|---|---|---|---|
|Refresh index|`-Sy`|`update`|`check-update`|`refresh`|`update`|`-S`|`--sync`|
|Install|`-S pkg`|`install pkg`|`install pkg`|`install pkg`|`add pkg`|`xbps-install pkg`|`-av pkg`|
|Remove|`-Rns pkg`|`remove pkg`|`remove pkg`|`remove pkg`|`del pkg`|`xbps-remove -R pkg`|`-C pkg`|
|Upgrade all|`-Syu`|`upgrade`|`upgrade`|`update`|`upgrade`|`xbps-install -Su`|`-avuDN @world`|
|Search|`-Ss text`|`search text`|`search text`|`search text`|`search text`|`xbps-query -Rs text`|`-s text`|
|Package info|`-Si pkg`|`show pkg`|`info pkg`|`info pkg`|`info pkg`|`xbps-query -R pkg`|`equery meta pkg`|
|List installed|`-Q`|`list --installed`|`list --installed`|`search -i`|`list -I`|`xbps-query -l`|`qlist -I`|
|Clean cache|`-Sc`|`clean`|`clean all`|`clean`|`cache clean`|`xbps-remove -O`|`eclean-dist`|

Short forms: pacman uses flags after `pacman`, apt/dnf/zypper/apk use `tool command`, xbps and emerge have their own command names (see each section).

---

# Arch family

## pacman

**Used in:** Arch, Manjaro, EndeavourOS, Garuda, CachyOS, Aurum OS **Type:** binary, official repos only (AUR goes through [yay](#yay) / [paru](#paru))

```bash
sudo pacman -Syu                 # full system upgrade (sync + upgrade)
sudo pacman -S pkg               # install
sudo pacman -S pkg1 pkg2         # install several
sudo pacman -Rns pkg             # remove + unused deps + config files
sudo pacman -R pkg               # remove only the package
pacman -Ss text                  # search in repos
pacman -Si pkg                   # info about repo package
pacman -Qi pkg                   # info about installed package
pacman -Q                        # list all installed
pacman -Qe                       # list explicitly installed (not deps)
pacman -Qdt                      # orphans (deps nobody needs anymore)
pacman -Ql pkg                   # files owned by installed package
pacman -Qo /path/to/file         # which package owns this file
sudo pacman -U file.pkg.tar.zst  # install from a local file
sudo pacman -Sc                  # remove old cached packages
sudo pacman -Fy && pacman -F file  # find which package provides a file
```

⚠️ Never run `pacman -Sy pkg` (refresh without upgrade) — it causes partial upgrades and can break the system. Use `-Syu` first, or `-Syu pkg`.

---

## yay

**Used in:** Arch-based distros **Type:** AUR helper, wraps pacman (builds from PKGBUILD)

```bash
yay                              # same as yay -Syu (repos + AUR)
yay -S pkg                       # install (repo or AUR)
yay -Ss text                     # search repos + AUR
yay -Si pkg                      # info
yay -Rns pkg                     # remove (same as pacman)
yay -Qua                         # list AUR packages that have updates
yay -Sua                         # upgrade AUR packages only
yay -Yc                          # remove unneeded dependencies
yay -G pkg                       # download PKGBUILD without building
yay -Sc                          # clean cache
```

⚠️ Do not run as root. Read the PKGBUILD of unknown AUR packages before installing.

---

## paru

**Used in:** Arch-based distros **Type:** AUR helper, written in Rust, wraps pacman

```bash
paru                             # same as paru -Syu
paru -S pkg                      # install (repo or AUR)
paru -Ss text                    # search
paru -Si pkg                     # info
paru -Rns pkg                    # remove
paru -Qua                        # AUR packages with updates
paru -Sua                        # upgrade AUR only
paru -c                          # remove unneeded dependencies
paru -G pkg                      # download PKGBUILD
paru -Gp pkg                     # print PKGBUILD to terminal (to read it)
paru -Sc                         # clean cache
```

---

# Debian family

## apt

**Used in:** Debian, Ubuntu, Linux Mint, Kali, Pop!_OS, Raspberry Pi OS **Type:** binary, front-end for [dpkg](#dpkg)

```bash
sudo apt update                  # refresh package index
sudo apt upgrade                 # upgrade installed packages
sudo apt full-upgrade            # upgrade, allowed to add/remove packages
sudo apt install pkg             # install
sudo apt install ./file.deb      # install local .deb with deps resolved
sudo apt remove pkg              # remove, keep configs
sudo apt purge pkg               # remove + configs
sudo apt autoremove              # remove unneeded deps
apt search text                  # search
apt show pkg                     # package info
apt list --installed             # installed packages
apt list --upgradable            # what can be upgraded
apt policy pkg                   # versions and repo priority
sudo apt clean                   # clean downloaded .deb cache
sudo apt --fix-broken install    # repair broken dependencies
```

---

## dpkg

**Used in:** Debian family **Type:** low-level, works with `.deb` files, does not resolve dependencies

```bash
sudo dpkg -i file.deb            # install a .deb
sudo dpkg -r pkg                 # remove (keep configs)
sudo dpkg -P pkg                 # purge (remove + configs)
dpkg -l                          # list installed
dpkg -l | grep text              # find among installed
dpkg -s pkg                      # status/info of installed package
dpkg -L pkg                      # files of installed package
dpkg -S /path/to/file            # which package owns the file
dpkg -c file.deb                 # list contents of a .deb
dpkg -I file.deb                 # info about a .deb
sudo dpkg --configure -a         # finish interrupted installs
```

If `dpkg -i` complains about missing deps, run `sudo apt --fix-broken install`.

---

# Red Hat family

## dnf

**Used in:** Fedora, RHEL 8+, CentOS Stream, Rocky, AlmaLinux **Type:** binary, successor of [yum](#yum)

```bash
sudo dnf check-update            # show available updates
sudo dnf upgrade                 # upgrade everything
sudo dnf install pkg             # install
sudo dnf install ./file.rpm      # install local rpm with deps
sudo dnf remove pkg              # remove
sudo dnf autoremove              # remove unneeded deps
dnf search text                  # search
dnf info pkg                     # info
dnf list --installed             # installed packages
dnf provides /path/to/file       # which package provides the file
dnf history                      # transaction history
sudo dnf history undo ID         # undo a transaction
sudo dnf group install "Group"   # install a package group
sudo dnf clean all               # clean cache
```

---

## yum

**Used in:** CentOS 7 and older RHEL **Type:** legacy; on RHEL 8+ `yum` is just an alias to [dnf](#dnf)

```bash
sudo yum check-update            # available updates
sudo yum update                  # upgrade everything
sudo yum install pkg             # install
sudo yum localinstall file.rpm   # install local rpm (older syntax)
sudo yum remove pkg              # remove
yum search text                  # search
yum info pkg                     # info
yum list installed               # installed packages
yum provides /path/to/file       # which package provides the file
yum history                      # transaction history
sudo yum clean all               # clean cache
```

---

## rpm

**Used in:** Red Hat family, SUSE, Mageia **Type:** low-level, works with `.rpm` files, no dependency resolution

```bash
sudo rpm -ivh file.rpm           # install (verbose + hash progress)
sudo rpm -Uvh file.rpm           # upgrade (or install if not present)
sudo rpm -e pkg                  # erase (remove)
rpm -qa                          # list all installed
rpm -qa | grep text              # find among installed
rpm -qi pkg                      # info about installed package
rpm -ql pkg                      # files of installed package
rpm -qf /path/to/file            # which package owns the file
rpm -qpl file.rpm                # list files inside a .rpm
rpm -q --requires pkg            # dependencies of a package
rpm -V pkg                       # verify files against the database
rpm -K file.rpm                  # check signature / checksum
sudo rpm --import key.asc        # import a GPG key
```

---

# SUSE

## zypper

**Used in:** openSUSE (Leap, Tumbleweed), SLES **Type:** binary, libzypp-based

```bash
sudo zypper refresh              # refresh repos        (short: ref)
sudo zypper update               # upgrade packages     (short: up)
sudo zypper dist-upgrade         # distro upgrade, required on Tumbleweed (short: dup)
sudo zypper install pkg          # install              (short: in)
sudo zypper remove pkg           # remove               (short: rm)
zypper search text               # search               (short: se)
zypper search -i text            # search among installed only
zypper info pkg                  # info
zypper what-provides file        # which package provides it
zypper repos                     # list repos           (short: lr)
sudo zypper addrepo URL name     # add repo             (short: ar)
sudo zypper clean                # clean cache
sudo zypper packages --unneeded  # list unneeded packages
```

---

# Mandriva / Mageia

## urpmi

**Used in:** Mandriva, older Mageia, ROSA **Type:** legacy; newer Mageia releases also provide [dnf](#dnf)

```bash
sudo urpmi.update -a             # update all media (repos)
sudo urpmi pkg                   # install
sudo urpme pkg                   # remove
urpmq -y text                    # fuzzy search
urpmq -i pkg                     # info
sudo urpmi --auto-select         # upgrade the whole system
sudo urpmi.addmedia name URL     # add a repo
rpm -qa                          # list installed (via rpm)
```

---

# PLD

## poldek

**Used in:** PLD Linux **Type:** legacy, rpm front-end with an interactive shell ⚠️ Rare tool, check `man poldek` for your version.

```bash
sudo poldek --up                 # update package indexes
sudo poldek -u pkg               # install / upgrade pkg
sudo poldek -e pkg               # erase (remove)
poldek                           # start interactive shell (use: search text, install pkg, ...)
```

---

# Void

## xbps

**Used in:** Void Linux **Type:** binary, several separate tools

```bash
sudo xbps-install -S             # sync repo index
sudo xbps-install -Su            # sync + upgrade all
sudo xbps-install pkg            # install
sudo xbps-install -Syu           # sync + upgrade, assume yes
sudo xbps-remove pkg             # remove pkg only
sudo xbps-remove -R pkg          # remove + unneeded deps
sudo xbps-remove -o              # remove orphans
sudo xbps-remove -O              # clean package cache
xbps-query -Rs text              # search in repos
xbps-query -R pkg                # info about repo package
xbps-query -l                    # list installed
xbps-query -f pkg                # files of a package
xbps-query -o /path/to/file      # which package owns the file (needs xtools: xlocate)
```

---

# Gentoo

## emerge

**Used in:** Gentoo (Portage) **Type:** source-based, compiles packages with USE flags

```bash
sudo emerge --sync               # sync the Portage tree
sudo emerge -avuDN @world        # full upgrade (ask, verbose, update, deep, newuse)
sudo emerge -av pkg              # install (ask first)
sudo emerge -C pkg               # unmerge (remove)
sudo emerge --ask --depclean     # remove unneeded packages
emerge -s text                   # search by name
emerge -S text                   # search in descriptions
emerge --info                    # system build info
equery list '*'                  # list installed (gentoolkit)
equery uses pkg                  # USE flags of a package (gentoolkit)
equery belongs /path/to/file     # which package owns the file (gentoolkit)
qlist -I                         # installed packages (portage-utils)
sudo eclean-dist                 # clean old distfiles (gentoolkit)
```

⚠️ Always read the plan before pressing Y: `-a` (ask) exists for this reason.

---

# Alpine

## apk

**Used in:** Alpine Linux, many Docker images **Type:** binary, very fast and small

```bash
sudo apk update                  # refresh index
sudo apk upgrade                 # upgrade installed packages
sudo apk add pkg                 # install
sudo apk add --no-cache pkg      # install without keeping the index (Docker style)
sudo apk del pkg                 # remove
apk search text                  # search
apk info pkg                     # package info
apk info -L pkg                  # files of a package
apk info -W /path/to/file        # which package owns the file
apk list -I                      # installed packages
sudo apk cache clean             # clean cache
```

---

# Slackware family

## slackpkg

**Used in:** Slackware **Type:** official network helper on top of the low-level tools (`installpkg`, `upgradepkg`, `removepkg`)

```bash
sudo slackpkg update gpg         # import the GPG key (first time)
sudo slackpkg update             # refresh the package list
sudo slackpkg install-new        # install new packages added to the release
sudo slackpkg upgrade-all        # upgrade everything
sudo slackpkg install pkg        # install
sudo slackpkg remove pkg         # remove
slackpkg search text             # search
slackpkg info pkg                # info
sudo slackpkg clean-system       # remove packages not in the official list
```

Low-level: `installpkg file.tgz`, `upgradepkg file.tgz`, `removepkg pkg`.

---

## slapt-get

**Used in:** Slackware, Salix and friends **Type:** apt-like tool with dependency resolution (works with third-party repos)

```bash
sudo slapt-get --update          # refresh package list
sudo slapt-get --upgrade         # upgrade installed packages
sudo slapt-get --install pkg     # install
sudo slapt-get --remove pkg      # remove
slapt-get --search text          # search
slapt-get --show pkg             # info
slapt-get --installed            # list installed
sudo slapt-get --clean           # clean package cache
```

---

## swaret

**Used in:** Slackware (older setups) **Type:** legacy, mostly replaced by [slackpkg](#slackpkg) and [slapt-get](#slapt-get) ⚠️ Check `swaret --help` for your version.

```bash
sudo swaret --update             # refresh the package list
sudo swaret --upgrade            # upgrade installed packages
sudo swaret --install pkg        # install
sudo swaret --remove pkg         # remove
swaret --search text             # search
swaret --list                    # list packages
```

---

## netpkg

**Used in:** Zenwalk (Slackware-based) **Type:** legacy, project is no longer active ⚠️ Rare tool. It has a text menu: just run `sudo netpkg`, and check `netpkg --help` for options.

```bash
sudo netpkg                      # open the interactive interface (update, install, remove)
sudo netpkg pkg                  # install pkg
```

---

# Solus / Pardus

## eopkg

**Used in:** Solus **Type:** binary, fork of [pisi](#pisi)

```bash
sudo eopkg update-repo           # refresh repos           (short: ur)
sudo eopkg upgrade               # upgrade all             (short: up)
sudo eopkg install pkg           # install                 (short: it)
sudo eopkg remove pkg            # remove                  (short: rm)
sudo eopkg remove-orphans        # remove orphans          (short: rmo)
eopkg search text                # search                  (short: sr)
eopkg info pkg                   # info
eopkg list-installed             # list installed          (short: li)
eopkg list-available             # list all available      (short: la)
sudo eopkg delete-cache          # clean cache             (short: dc)
sudo eopkg history               # transaction history
sudo eopkg add-repo name URL     # add repo                (short: ar)
```

---

## pisi

**Used in:** Pardus (old), PiSi Linux **Type:** original of [eopkg](#eopkg), same command style

```bash
sudo pisi update-repo            # refresh repos           (short: ur)
sudo pisi upgrade                # upgrade all             (short: up)
sudo pisi install pkg            # install                 (short: it)
sudo pisi remove pkg             # remove                  (short: rm)
pisi search text                 # search                  (short: sr)
pisi info pkg                    # info
pisi list-installed              # list installed          (short: li)
sudo pisi add-repo name URL      # add repo                (short: ar)
```

---

# Source-based minimalist

## kiss

**Used in:** KISS Linux **Type:** source-based, every package is built locally; project is unmaintained upstream

```bash
kiss update                      # pull repo changes (then upgrade packages)   (short: u)
kiss build pkg                   # build pkg                                   (short: b)
kiss install pkg                 # install built package                       (short: i)
kiss remove pkg                  # remove                                      (short: r)
kiss search text                 # search                                      (short: s)
kiss list                        # list installed                              (short: l)
kiss download pkg                # download sources only                       (short: d)
kiss checksum pkg                # generate checksums                          (short: c)
```

Typical flow: `kiss b pkg && kiss i pkg`.

---

## cpt

**Used in:** Carbs Linux **Type:** source-based, fork of [kiss](#kiss), the same syntax

```bash
cpt update                       # update repos and upgrade packages
cpt build pkg                    # build pkg
cpt install pkg                  # install built package
cpt remove pkg                   # remove
cpt search text                  # search
cpt list                         # list installed
cpt download pkg                 # download sources only
cpt checksum pkg                 # generate checksums
```

---

# Tiny Core

## tce

**Used in:** Tiny Core Linux (`tce-load` and related tools) **Type:** binary extensions (`.tcz`), loaded into RAM ⚠️ By default everything lives in RAM. To keep an extension after reboot it must be in `/tce/optional` and listed in `/tce/onboot.lst`.

```bash
tce-load -wi pkg                 # download (w) + install (i)
tce-load -w pkg                  # download only
tce-load -i pkg                  # install an already downloaded extension
tce-ab                           # Apps Browser (text UI): search, info, install
tce-update                       # update installed extensions
tce-status -i                    # list installed extensions
tce-status -u                    # list downloaded but not installed
```

---

# Foresight

## conary

**Used in:** Foresight Linux, rPath **Type:** legacy, project is discontinued

```bash
sudo conary update pkg           # install or update pkg
sudo conary updateall            # update the whole system
sudo conary erase pkg            # remove
conary rq text                   # search in repos (repquery)
conary q                         # list installed (query)
conary q pkg --info              # info about installed package
sudo conary rollback ID          # roll back a changeset
```

---

# Clear Linux

## swupd

**Used in:** Clear Linux OS **Type:** bundle-based (you install bundles, not individual packages) ⚠️ Clear Linux was discontinued by Intel in July 2025, so no new updates.

```bash
sudo swupd update                # update the OS
sudo swupd check-update          # check if an update is available
sudo swupd bundle-add name       # install a bundle
sudo swupd bundle-remove name    # remove a bundle
swupd bundle-list                # list installed bundles
swupd bundle-list --all          # list all available bundles
swupd search text                # find which bundle contains a file
sudo swupd diagnose              # check the system files
sudo swupd repair                # fix broken files
```

---

# BSD

## pkgsrc

**Used in:** NetBSD (native), also macOS, Linux, illumos, SmartOS **Type:** ports-like collection + `pkgin` as a binary front-end

```bash
# --- binary packages with pkgin ---
sudo pkgin update                # refresh index
sudo pkgin install pkg           # install
sudo pkgin remove pkg            # remove
sudo pkgin upgrade               # upgrade installed packages
sudo pkgin full-upgrade          # upgrade including version-changed deps
pkgin search text                # search
pkgin list                       # list installed
sudo pkgin autoremove            # remove unneeded deps
sudo pkgin clean                 # clean cache

# --- from source ---
cd /usr/pkgsrc/category/pkg
sudo make install clean          # build + install + cleanup

# --- low-level tools ---
pkg_info                         # list installed
sudo pkg_delete pkg              # remove
```

---

## ports

**Used in:** FreeBSD (and the ports idea in OpenBSD / DragonFly) **Type:** binary (`pkg`) and source (ports tree)

```bash
# --- FreeBSD binary packages ---
sudo pkg update                  # refresh index
sudo pkg install pkg             # install
sudo pkg delete pkg              # remove
sudo pkg upgrade                 # upgrade everything
sudo pkg autoremove              # remove unneeded deps
pkg search text                  # search
pkg info pkg                     # info
pkg info                         # list installed
sudo pkg clean                   # clean cache

# --- FreeBSD ports (from source) ---
sudo git clone https://git.FreeBSD.org/ports.git /usr/ports   # get the tree (first time)
cd /usr/ports/category/pkg
sudo make install clean          # configure, build, install, cleanup
sudo make deinstall              # remove

# --- OpenBSD equivalent ---
doas pkg_add pkg                 # install
doas pkg_delete pkg              # remove
doas pkg_add -u                  # upgrade all
pkg_info -Q text                 # search
```

---

# Functional / declarative

## nix

**Used in:** NixOS, and on top of any Linux / macOS **Type:** functional, packages live in `/nix/store`, atomic rollbacks

```bash
# --- new CLI (flakes) ---
nix search nixpkgs text          # search
nix profile install nixpkgs#pkg  # install into your profile
nix profile list                 # list installed
nix profile remove pkg           # remove
nix profile upgrade '.*'         # upgrade all
nix shell nixpkgs#pkg            # temporary shell with pkg, nothing installed
nix run nixpkgs#pkg              # run once without installing

# --- classic CLI ---
nix-channel --update             # refresh channels
nix-env -iA nixpkgs.pkg          # install
nix-env -e pkg                   # remove
nix-env -u                       # upgrade all
nix-env -q                       # list installed
nix-env -qaP | grep text         # search

# --- cleanup ---
nix-collect-garbage -d           # delete old generations + unused store paths

# --- NixOS only ---
sudo nixos-rebuild switch        # apply /etc/nixos/configuration.nix
sudo nixos-rebuild switch --rollback   # go back to the previous generation
```

⚠️ On NixOS the "right way" is to put packages in `configuration.nix`, not `nix-env`.

---

## guix

**Used in:** Guix System, and on top of other distros **Type:** functional, like Nix but configured with Scheme (Guile)

```bash
guix pull                        # update Guix itself and the package definitions
guix install pkg                 # install
guix remove pkg                  # remove
guix package -u                  # upgrade all installed packages
guix search text                 # search
guix show pkg                    # package info
guix package -I                  # list installed
guix shell pkg                   # temporary environment with pkg
guix package --roll-back         # undo the last profile change
guix gc                          # garbage collection
```

---

# Universal (sandboxed apps)

## flatpak

**Used in:** any distro (Fedora Silverblue, Steam Deck, Linux Mint, ...) **Type:** sandboxed desktop apps, usually from Flathub

```bash
flatpak remote-add --if-not-exists flathub https://dl.flathub.org/repo/flathub.flatpakrepo   # add Flathub (once)
flatpak search text              # search
flatpak install flathub org.app.Name    # install
flatpak run org.app.Name         # run
flatpak update                   # update all apps and runtimes
flatpak uninstall org.app.Name   # remove
flatpak uninstall --unused       # remove unused runtimes
flatpak list                     # list installed
flatpak info org.app.Name        # info
flatpak override --user --filesystem=home org.app.Name   # change permissions
```

---

## snap

**Used in:** Ubuntu (preinstalled), available on most distros **Type:** sandboxed apps from the Snap Store (Canonical), auto-updates in the background

```bash
snap find text                   # search
sudo snap install pkg            # install
sudo snap install pkg --classic  # install with full system access (IDEs, etc.)
sudo snap refresh                # update all now
sudo snap refresh pkg            # update one
sudo snap remove pkg             # remove
snap list                        # list installed
snap info pkg                    # info
sudo snap revert pkg             # go back to the previous version
snap services                    # list snap services
```

---

# Language ecosystems

These do **not** replace the system package manager: they install libraries and tools for one language.

## cargo

**Language:** Rust **Where it installs:** binaries to `~/.cargo/bin` (add it to `PATH`)

```bash
cargo install crate              # build and install a binary crate
cargo install --locked crate     # same, using the crate's own Cargo.lock
cargo uninstall crate            # remove
cargo install --list             # list installed binaries
cargo search text                # search crates.io

# --- inside a project ---
cargo add crate                  # add a dependency to Cargo.toml
cargo remove crate               # remove a dependency
cargo update                     # update dependencies in Cargo.lock
cargo build                      # build the project
cargo build --release            # optimized build
cargo run                        # build + run
```

Extra tools (installed via cargo itself): `cargo install cargo-update` then `cargo install-update -a` updates all installed binaries.

---

## pip

**Language:** Python **Where it installs:** system site-packages, `~/.local`, or a virtual environment

```bash
python -m venv .venv             # create a virtual environment
source .venv/bin/activate        # activate it (Linux/macOS)
pip install pkg                  # install
pip install pkg==1.2.3           # install exact version
pip install --upgrade pkg        # upgrade
pip uninstall pkg                # remove
pip list                         # list installed
pip list --outdated              # what can be updated
pip show pkg                     # info
pip freeze > requirements.txt    # save installed versions
pip install -r requirements.txt  # install from a file
pip cache purge                  # clean cache

# --- CLI tools without polluting the system ---
pipx install pkg                 # install a tool in its own isolated env
pipx upgrade-all                 # upgrade all
```

⚠️ On Arch, Debian 12+, Ubuntu 23.04+ and others `pip install` outside a venv gives `externally-managed-environment`. This is intended (PEP 668): use a venv, `pipx`, or the system package (`pacman -S python-pkg`).

---

## npm

**Language:** JavaScript / Node.js **Default tool** bundled with Node.js

```bash
npm init -y                      # create package.json
npm install pkg                  # install into the project (node_modules)
npm install -D pkg               # install as a dev dependency
npm install -g pkg               # install globally
npm install                      # install everything from package.json
npm ci                           # clean install from package-lock.json (CI)
npm uninstall pkg                # remove
npm update                       # update within allowed version ranges
npm outdated                     # what can be updated
npm ls --depth=0                 # list project packages
npm ls -g --depth=0              # list global packages
npm search text                  # search
npm view pkg                     # info about a package in the registry
npx pkg                          # run a package without installing it globally
npm cache clean --force          # clean cache
```

---

## yarn

**Language:** JavaScript / Node.js **Note:** two generations with different commands: Classic (v1) and Berry (v2+)

```bash
yarn init                        # create package.json
yarn add pkg                     # add a dependency
yarn add -D pkg                  # add as a dev dependency
yarn remove pkg                  # remove
yarn install                     # install everything from package.json
yarn outdated                    # what can be updated (Classic)
yarn why pkg                     # why this package is installed
yarn upgrade                     # update dependencies (Classic)
yarn up pkg                      # update a dependency (Berry)
yarn global add pkg              # global install (Classic only)
```

⚠️ Berry (v2+) has no `yarn global`. Use `npm i -g`, `pnpm add -g`, or `yarn dlx pkg` for one-off runs.

---

## pnpm

**Language:** JavaScript / Node.js **Note:** one shared store on disk, projects use hard links, so it saves space and is fast

```bash
pnpm setup                       # configure global bin directory (first time)
pnpm init                        # create package.json
pnpm add pkg                     # add a dependency
pnpm add -D pkg                  # add as a dev dependency
pnpm add -g pkg                  # install globally
pnpm install                     # install everything from package.json
pnpm remove pkg                  # remove
pnpm update                      # update dependencies
pnpm outdated                    # what can be updated
pnpm list                        # list installed
pnpm dlx pkg                     # run without installing (like npx)
pnpm store prune                 # remove unreferenced packages from the store
```

---

## gem

**Language:** Ruby **Where it installs:** system gems, or `~/.local/share/gem` with `--user-install`

```bash
gem install pkg                  # install
gem install --user-install pkg   # install into the user directory (no sudo)
gem uninstall pkg                # remove
gem update                       # update all gems
gem update pkg                   # update one
gem list                         # list installed
gem search text                  # search
gem info pkg                     # info
gem outdated                     # what can be updated
gem cleanup                      # remove old versions
gem env                          # show gem paths and config

# --- inside a project ---
bundle install                   # install gems from Gemfile
bundle update                    # update gems from Gemfile
bundle exec cmd                  # run a command in the Gemfile environment
```

---

## composer

**Language:** PHP **Where it installs:** project `vendor/` directory (or `~/.composer` for global)

```bash
composer init                    # create composer.json
composer require vendor/pkg      # add a dependency
composer require --dev vendor/pkg    # add as a dev dependency
composer remove vendor/pkg       # remove
composer install                 # install from composer.lock
composer update                  # update dependencies
composer outdated                # what can be updated
composer show                    # list installed
composer show vendor/pkg         # info about one package
composer search text             # search on Packagist
composer global require vendor/pkg   # install a tool globally
composer dump-autoload           # rebuild the autoloader
composer self-update             # update Composer itself
```

---

# Unclear

## upkg

⚠️ I could not match `upkg` to one well-known package manager, so I did not invent commands for it.

**TODO:** decide which tool you mean and fill this section:

- a universal wrapper that gives one syntax over many managers (such as `upt`)
- a specific distro or project tool called `upkg`
- a typo of another entry from this list

Once the tool is known, use the same template as above: **Used in / Type**, then a code block with refresh, install, remove, upgrade, search, info, list installed, clean cache.

---

## Notes for maintaining this file

- Every section follows the same template: header, `Used in`, `Type`, one code block, optional ⚠️ warning.
- Keep comments in English: the file is shared between `eng/` and `ru/`.
- Legacy or rare tools are marked ⚠️ because commands may differ between versions; verify with `--help` or `man`.