# Aurora Customizations Analysis

**Comprehensive list of all customizations Aurora makes to a baseline Fedora Kinoite image**

This document catalogs every modification Aurora applies on top of the standard Fedora Atomic KDE (Kinoite) image, organized by Blue Build module types. Use this as a reference when deciding which customizations to keep, modify, or remove for your custom image.

> **Base Image**: `ghcr.io/ublue-os/kinoite-main` (Universal Blue's Kinoite)
> **Source Repository**: https://github.com/ublue-os/aurora

---

## Table of Contents

1. [akmods (Kernel Modules)](#1-akmods-kernel-modules)
2. [rpm-ostree / dnf (Package Management)](#2-rpm-ostree--dnf-package-management)
3. [default-flatpaks](#3-default-flatpaks)
4. [files (System Files & Configurations)](#4-files-system-files--configurations)
5. [systemd (Services)](#5-systemd-services)
6. [initramfs](#6-initramfs)
7. [os-release (Image Metadata)](#7-os-release-image-metadata)
8. [firmware](#8-firmware)
9. [branding](#9-branding)
10. [script (Build Scripts)](#10-script-build-scripts)
11. [kargs (Kernel Arguments)](#11-kargs-kernel-arguments)
12. [fonts](#12-fonts)
13. [justfiles](#13-justfiles)
14. [brew (Homebrew)](#14-brew-homebrew)
15. [DX (Developer Experience) Variant](#15-dx-developer-experience-variant)

---

## 1. akmods (Kernel Modules)

**Source**: `build_files/base/03-install-kernel-akmods.sh`

Aurora installs additional kernel modules (akmods) for enhanced hardware support.

### Base Akmods (All Variants)

| Module | Description | Keep? |
|--------|-------------|-------|
| `xone-kmod` | Xbox One controller wireless adapter support | ☐ |
| `xpadneo-kmod` | Xbox One controller Bluetooth support with advanced features | ☐ |
| `openrazer-kmod` | Razer peripheral RGB and device support | ☐ |
| `framework-laptop-kmod` | Framework laptop hardware support | ☐ |
| `v4l2loopback` | Virtual video device (for OBS virtual camera, etc.) | ☐ |

### Nvidia Variant Akmods (nvidia-open flavor only)

| Module | Description | Keep? |
|--------|-------------|-------|
| `nvidia-driver` | Nvidia proprietary driver (open kernel module variant) | ☐ |
| `nvidia-driver-cuda` | CUDA support for Nvidia GPUs | ☐ |
| `nvidia-modprobe` | Nvidia module loader utility | ☐ |
| `nvidia-persistenced` | Nvidia persistence daemon | ☐ |
| `nvidia-settings` | Nvidia control panel GUI | ☐ |
| `nvidia-vaapi-driver` | VA-API video acceleration for Nvidia | ☐ |
| `libva-nvidia-driver` | VA-API backend for Nvidia | ☐ |
| `libnvidia-fbc` | Nvidia frame buffer capture library | ☐ |
| `libnvidia-ml.i686` | 32-bit Nvidia management library | ☐ |

### ZFS Support (Stable Stream Only)

| Module | Description | Keep? |
|--------|-------------|-------|
| `zfs` | ZFS filesystem kernel module | ☐ |

**Blue Build Module Equivalent**:
```yaml
- type: akmods
  install:
    - xone
    - xpadneo
    - openrazer
    - framework-laptop
    - v4l2loopback
  # For Nvidia variant:
  nvidia-driver: nvidia-open
```

---

## 2. rpm-ostree / dnf (Package Management)

**Source**: `build_files/base/04-packages.sh`

### Fedora Repository Packages (Installed)

These packages are installed from official Fedora repositories.

#### System & Hardware

| Package | Description | Keep? |
|---------|-------------|-------|
| `mesa-va-drivers-freeworld` | VA-API video acceleration (AMD/Intel) | ☐ |
| `mesa-vdpau-drivers-freeworld` | VDPAU video acceleration | ☐ |
| `libva-utils` | VA-API utilities and diagnostics | ☐ |
| `vulkan-tools` | Vulkan graphics API tools | ☐ |
| `alsa-firmware` | ALSA audio firmware | ☐ |
| `alsa-plugins-pulseaudio` | PulseAudio ALSA plugin | ☐ |
| `intel-media-driver` | Intel Quick Sync Video driver | ☐ |
| `libva-intel-driver` | Intel VA-API driver (legacy) | ☐ |
| `lm_sensors` | Hardware monitoring sensors | ☐ |
| `fwupd` | Firmware update daemon | ☐ |
| `thermald` | Intel thermal daemon | ☐ |
| `ddcutil` | Monitor control via DDC/CI | ☐ |
| `dmidecode` | DMI/SMBIOS hardware info | ☐ |
| `input-remapper` | Input device remapping tool | ☐ |
| `libcamera` | Camera stack library | ☐ |
| `libcamera-gstreamer` | GStreamer libcamera plugin | ☐ |
| `libcamera-tools` | libcamera utilities | ☐ |
| `pipewire-codec-aptx` | aptX Bluetooth codec for PipeWire | ☐ |

#### Networking & Connectivity

| Package | Description | Keep? |
|---------|-------------|-------|
| `NetworkManager-openvpn` | OpenVPN support for NetworkManager | ☐ |
| `NetworkManager-wifi` | WiFi support for NetworkManager | ☐ |
| `NetworkManager-wwan` | Mobile broadband support | ☐ |
| `iwlwifi-dvm-firmware` | Intel WiFi DVM firmware | ☐ |
| `iwlwifi-mvm-firmware` | Intel WiFi MVM firmware | ☐ |
| `realtek-firmware` | Realtek firmware | ☐ |

#### Filesystems & Storage

| Package | Description | Keep? |
|---------|-------------|-------|
| `bcachefs-tools` | bcachefs filesystem tools | ☐ |
| `btrfs-progs` | Btrfs filesystem utilities | ☐ |
| `compsize` | Btrfs compression statistics | ☐ |
| `dosfstools` | FAT filesystem tools | ☐ |
| `e2fsprogs` | ext2/3/4 filesystem tools | ☐ |
| `exfatprogs` | exFAT filesystem tools | ☐ |
| `f2fs-tools` | F2FS filesystem tools | ☐ |
| `ntfs-3g` | NTFS read/write support | ☐ |
| `xfsprogs` | XFS filesystem tools | ☐ |
| `gvfs-mtp` | MTP support for GVfs | ☐ |
| `gvfs-nfs` | NFS support for GVfs | ☐ |
| `gvfs-smb` | Samba/SMB support for GVfs | ☐ |
| `nfs-utils` | NFS utilities | ☐ |
| `samba-client` | Samba client | ☐ |
| `samba-common` | Samba common files | ☐ |
| `samba-usershares` | Samba user shares support | ☐ |

#### Terminal & Shell

| Package | Description | Keep? |
|---------|-------------|-------|
| `bash-completion` | Bash tab completion | ☐ |
| `fish` | Fish shell | ☐ |
| `zsh` | Z shell | ☐ |
| `fastfetch` | System info fetch tool | ☐ |
| `htop` | Interactive process viewer | ☐ |
| `btop` | Modern resource monitor | ☐ |
| `nvtop` | GPU process monitor | ☐ |
| `tmux` | Terminal multiplexer | ☐ |
| `wl-clipboard` | Wayland clipboard utilities | ☐ |
| `fzf` | Fuzzy finder | ☐ |
| `fd-find` | Modern `find` replacement | ☐ |
| `ripgrep` | Fast grep replacement | ☐ |
| `eza` | Modern `ls` replacement | ☐ |
| `bat` | Modern `cat` with syntax highlighting | ☐ |
| `yq` | YAML processor | ☐ |
| `jq` | JSON processor | ☐ |

#### Security & Authentication

| Package | Description | Keep? |
|---------|-------------|-------|
| `fprintd` | Fingerprint daemon | ☐ |
| `fprintd-pam` | PAM module for fingerprint auth | ☐ |
| `libfido2` | FIDO2 authentication library | ☐ |
| `pam-u2f` | PAM module for U2F/FIDO2 | ☐ |
| `pamu2fcfg` | U2F PAM configuration tool | ☐ |
| `fido2-tools` | FIDO2 utilities | ☐ |
| `tpm2-tools` | TPM 2.0 tools | ☐ |
| `clevis` | Automated disk decryption | ☐ |
| `clevis-luks` | Clevis LUKS integration | ☐ |
| `clevis-pin-tpm2` | Clevis TPM2 pin | ☐ |

#### Containers & Virtualization

| Package | Description | Keep? |
|---------|-------------|-------|
| `distrobox` | Container-based Linux distributions | ☐ |
| `podman` | Daemonless container engine | ☐ |
| `toolbox` | Fedora container toolbox | ☐ |
| `bootc` | Bootable container management | ☐ |
| `qemu-user-static-aarch64` | QEMU ARM64 user emulation | ☐ |
| `qemu-user-static-arm` | QEMU ARM user emulation | ☐ |
| `qemu-user-static-x86` | QEMU x86 user emulation | ☐ |

#### KDE/Plasma Specific

| Package | Description | Keep? |
|---------|-------------|-------|
| `kde-inotify-survey` | KDE inotify usage monitor | ☐ |
| `kdeplasma-addons` | Additional Plasma widgets | ☐ |
| `kfind` | KDE file search | ☐ |
| `krfb` | KDE Remote Desktop server | ☐ |
| `ksshaskpass` | SSH askpass for KDE | ☐ |
| `plasma-discover-notifier` | Discover update notifier | ☐ |
| `plasma-wallpapers-dynamic` | Dynamic wallpaper support | ☐ |
| `libappindicator-gtk3` | Application indicators | ☐ |
| `krdp` | KDE RDP support | ☐ |
| `wayland-utils` | Wayland utilities | ☐ |

#### Utilities & Tools

| Package | Description | Keep? |
|---------|-------------|-------|
| `p7zip` | 7-Zip archiver | ☐ |
| `p7zip-plugins` | 7-Zip format plugins | ☐ |
| `unrar` | RAR archive extractor | ☐ |
| `firewall-config` | Firewall configuration GUI | ☐ |
| `rclone` | Cloud storage sync tool | ☐ |
| `rsync` | Remote file sync | ☐ |
| `restic` | Backup program | ☐ |
| `smartmontools` | S.M.A.R.T. disk monitoring | ☐ |
| `stress-ng` | System stress testing | ☐ |
| `wlsunset` | Wayland night light | ☐ |
| `evtest` | Input device testing | ☐ |

### COPR Repository Packages

These packages come from COPR (Community Projects) repositories.

| COPR Repository | Package | Description | Keep? |
|-----------------|---------|-------------|-------|
| `ublue-os/staging` | `ublue-os-update-services` | Aurora update automation | ☐ |
| `ublue-os/staging` | `ublue-bling` | Shell enhancements (bling.sh) | ☐ |
| `ublue-os/staging` | `ublue-os-just` | Justfile system management | ☐ |
| `ublue-os/staging` | `ujust` | User-facing just commands | ☐ |
| `ublue-os/staging` | `ublue-os-luks` | LUKS encryption helpers | ☐ |
| `kylegospo/system76-scheduler` | `system76-scheduler` | CPU scheduler optimization | ☐ |
| `ublue-os/bling` | `ptyxis` | Modern terminal emulator | ☐ |
| `ublue-os/packages` | `topgrade` | Update everything tool | ☐ |
| `ublue-os/packages` | `bootupd` | Bootloader update daemon | ☐ |
| `ublue-os/flatpak-test` | `flatpak`, `flatpak-libs` | Custom Flatpak builds | ☐ |

### Excluded/Removed Packages

These packages are explicitly removed from the base Kinoite image.

| Package | Reason for Removal | Re-add? |
|---------|-------------------|---------|
| `firefox` | Replaced with Flatpak version | ☐ |
| `firefox-langpacks` | Not needed with Flatpak Firefox | ☐ |
| `htop` (Kinoite version) | Replaced with custom version | ☐ |
| `nvtop` (Kinoite version) | Replaced with custom version | ☐ |
| `plasma-discover-flatpak` | Replaced with Bazaar | ☐ |
| `plasma-discover-rpm-ostree` | Not needed | ☐ |
| `plasma-welcome` | Custom welcome experience | ☐ |

**Blue Build Module Equivalent**:
```yaml
- type: rpm-ostree
  install:
    - mesa-va-drivers-freeworld
    - distrobox
    - fish
    # ... etc
  remove:
    - firefox
    - firefox-langpacks
    - plasma-discover-flatpak
```

---

## 3. default-flatpaks

**Source**: `build_files/base/17-cleanup.sh`, `system_files/shared/usr/share/flatpak/preinstall.d/`

Aurora configures Flathub as the primary Flatpak remote and removes Fedora's Flatpak repos.

### Flatpak Remote Configuration

| Configuration | Value | Description |
|---------------|-------|-------------|
| Primary Remote | `flathub` | Flathub is added and set as default |
| Fedora Remote | **Removed** | `flatpak-nuke-fedora.service` removes Fedora repos |

### Pre-installed Flatpaks

| Flatpak ID | Application | Description | Keep? |
|------------|-------------|-------------|-------|
| `io.github.kolunmi.Bazaar` | Bazaar | Flatpak application browser/installer | ☐ |

> **Note**: Most Flatpak applications are listed in external configuration files from the `common` image (`ghcr.io/get-aurora-dev/common:latest`). Core KDE apps like Haruna, Gwenview, etc. are pre-installed via these external lists.

**Blue Build Module Equivalent**:
```yaml
- type: default-flatpaks
  notify: true
  system:
    install:
      - io.github.kolunmi.Bazaar
    repo-url: https://flathub.org/repo/flathub.flatpakrepo
    repo-name: flathub
```

---

## 4. files (System Files & Configurations)

**Source**: `system_files/shared/`

### Configuration Files

| File Path | Description | Keep? |
|-----------|-------------|-------|
| `/etc/rpm-ostreed.conf` | Sets `AutomaticUpdatePolicy=stage` for staged updates | ☐ |
| `/usr/lib/bootc/install/20-aurora.toml` | Sets `root-fs-type = "btrfs"` for bootc installations | ☐ |
| `/usr/lib/modprobe.d/amd-legacy.conf` | Enables legacy AMD GPU support (SI/CIK) with amdgpu driver | ☐ |
| `/usr/lib/tmpfiles.d/usr-share-sddm-themes.conf` | Creates SDDM themes directory | ☐ |

### AMD Legacy GPU Support (`amd-legacy.conf`)

```conf
options amdgpu si_support=1
options amdgpu cik_support=1
options radeon si_support=0
options radeon cik_support=0
```

This enables the modern amdgpu driver for older Southern Islands (HD 7000) and Sea Islands (HD 8000/R5/R7/R9 200) GPUs.

**Blue Build Module Equivalent**:
```yaml
- type: files
  files:
    - source: system/etc
      destination: /etc
    - source: system/usr
      destination: /usr
```

---

## 5. systemd (Services)

**Source**: `build_files/base/17-cleanup.sh`, `system_files/shared/usr/lib/systemd/system/`

### Enabled Services

| Service | Description | Keep? |
|---------|-------------|-------|
| `flatpak-preinstall.service` | Pre-installs Flatpaks from `/usr/share/flatpak/preinstall.d/` | ☐ |
| `flatpak-nuke-fedora.service` | Removes Fedora Flatpak remotes on first boot | ☐ |
| `fstrim.timer` | Periodic SSD TRIM | ☐ |
| `dconf-update.service` | Updates dconf database | ☐ |
| `system76-scheduler.service` | CPU scheduler optimization | ☐ |
| `ublue-system-setup.service` | Aurora system setup | ☐ |
| `ublue-guest-user.service` | Guest user management | ☐ |

### Disabled Services

| Service | Reason | Re-enable? |
|---------|--------|------------|
| `rpm-ostree-countme.timer` | Privacy - disables Fedora usage counting | ☐ |
| `bootloader-update.service` | Managed differently by Aurora | ☐ |

### Mount Units

| Unit | Description | Keep? |
|------|-------------|-------|
| `usr-share-sddm-themes.mount` | Bind mounts SDDM themes directory | ☐ |

**Blue Build Module Equivalent**:
```yaml
- type: systemd
  system:
    enabled:
      - flatpak-preinstall.service
      - fstrim.timer
      - system76-scheduler.service
    disabled:
      - rpm-ostree-countme.timer
```

---

## 6. initramfs

**Source**: `build_files/base/19-initramfs.sh`

Aurora regenerates the initramfs with additional dracut modules for enhanced security and authentication.

### Dracut Modules Added

| Module | Description | Keep? |
|--------|-------------|-------|
| `ostree` | OSTree boot support (required) | ☑ |
| `fido2` | FIDO2/WebAuthn hardware key support for LUKS | ☐ |
| `tpm2-tss` | TPM 2.0 support for disk encryption | ☐ |
| `pkcs11` | PKCS#11 smartcard support | ☐ |
| `pcsc` | PC/SC smartcard daemon support | ☐ |

**Blue Build Module Equivalent**:
```yaml
- type: initramfs
  modules:
    - fido2
    - tpm2-tss
    - pkcs11
    - pcsc
```

---

## 7. os-release (Image Metadata)

**Source**: `build_files/base/00-image-info.sh`

Aurora customizes `/usr/lib/os-release` with its branding.

### Modified Fields

| Field | Aurora Value | Kinoite Default |
|-------|--------------|-----------------|
| `NAME` | Aurora | Fedora Linux |
| `ID` | aurora | fedora |
| `VARIANT_ID` | aurora / aurora-dx | kinoite |
| `PRETTY_NAME` | Aurora (Fedora Kinoite) | Fedora Linux XX (Kinoite) |
| `HOME_URL` | https://getaurora.dev | https://fedoraproject.org/ |
| `DOCUMENTATION_URL` | https://docs.getaurora.dev | Fedora docs |
| `SUPPORT_URL` | https://github.com/ublue-os/aurora/issues | Fedora support |

### Image Flavor Detection

The `IMAGE_FLAVOR` is automatically set based on `IMAGE_NAME`:
- Contains `nvidia` → `nvidia-open`
- Otherwise → `main`

**Blue Build Module Equivalent**:
```yaml
- type: os-release
  overrides:
    NAME: "My Custom OS"
    ID: my-os
    HOME_URL: "https://example.com"
```

---

## 8. firmware

**Source**: `build_files/base/08-firmware.sh`

Aurora downloads and installs additional firmware blobs not included in Fedora.

### Additional Firmware

| Firmware | Device | Location | Keep? |
|----------|--------|----------|-------|
| `WIFI_MT7922_patch_mcu_1_1_hdr.bin` | MediaTek MT7922 WiFi | `/usr/lib/firmware/mediatek/` | ☐ |
| `WIFI_RAM_CODE_MT7922_1.bin` | MediaTek MT7922 WiFi | `/usr/lib/firmware/mediatek/` | ☐ |

> These firmware files enable MediaTek MT7922 WiFi adapters that may not work with standard Fedora firmware packages.

**Blue Build Module Equivalent**:
```yaml
- type: script
  scripts:
    - download-firmware.sh
```

---

## 9. branding

**Source**: `build_files/base/05-branding.sh`

Aurora applies custom visual branding throughout the system.

### Branding Elements

| Element | Description | Keep? |
|---------|-------------|-------|
| Default Wallpaper | Aurora-themed wallpaper | ☐ |
| Plymouth Logo | Boot splash logo generated from SVG | ☐ |
| SDDM Theme | Login screen customization | ☐ |
| Panel Task Manager | KDE task manager default pinned apps | ☐ |
| System Logos | SVG logos converted to various sizes | ☐ |

### Wallpaper Configuration

Aurora sets a custom default wallpaper for KDE Plasma and configures it as the SDDM background.

### Plymouth Theme

The Plymouth boot splash uses Aurora branding, with logos generated at runtime from SVG source files using ImageMagick.

**Blue Build Module Equivalent**:
```yaml
- type: files
  files:
    - source: branding/wallpapers
      destination: /usr/share/wallpapers
    - source: branding/plymouth
      destination: /usr/share/plymouth/themes
```

---

## 10. script (Build Scripts)

**Source**: `build_files/base/06-override-install.sh`

Aurora runs various custom scripts during the build process.

### Override Installations

| Installation | Description | Keep? |
|--------------|-------------|-------|
| Starship | Cross-shell prompt with custom theme | ☐ |
| Nerd Fonts | Developer fonts with icons (symbols only) | ☐ |
| Bash Prexec | Bash preexec/precmd hooks | ☐ |
| Aurora CLI tools | Shell helper scripts (`bling.sh`, `bling.fish`) | ☐ |

### Discover Modifications

| Modification | Description | Keep? |
|--------------|-------------|-------|
| Hidden `.desktop` entries | Hides Discover installation prompts for certain apps | ☐ |

### Samba Configuration

Aurora pre-configures Samba for easy user share setup:
- Creates `/var/lib/samba/usershares` directory
- Sets proper permissions for user sharing

### GSchema Overrides

Aurora compiles custom GSchema overrides to set GNOME/GTK application defaults.

**Blue Build Module Equivalent**:
```yaml
- type: script
  scripts:
    - install-starship.sh
    - configure-samba.sh
```

---

## 11. kargs (Kernel Arguments)

Aurora does not appear to set custom kernel arguments by default. The base Universal Blue image handles necessary kargs.

**Blue Build Module Equivalent** (if needed):
```yaml
- type: kargs
  add:
    - "quiet"
    - "splash"
```

---

## 12. fonts

**Source**: `build_files/base/06-override-install.sh`

### Installed Fonts

| Font | Description | Keep? |
|------|-------------|-------|
| Nerd Fonts Symbols | Icon font for terminal prompts | ☐ |

> Full Nerd Fonts are available via Homebrew Brewfiles in Aurora-DX.

**Blue Build Module Equivalent**:
```yaml
- type: fonts
  fonts:
    nerd-fonts:
      - Symbols
```

---

## 13. justfiles

**Source**: Embedded in `ublue-os-just` package

Aurora includes system management commands via the `just` command runner.

### Available Just Commands (Examples)

| Command | Description | Keep? |
|---------|-------------|-------|
| `just update` | Update system, Flatpaks, and containers | ☐ |
| `just bios` | Reboot into BIOS/UEFI setup | ☐ |
| `just changelogs` | View Aurora changelogs | ☐ |
| `just distrobox-*` | Distrobox management commands | ☐ |
| `just setup-*` | Various setup wizards | ☐ |

**Blue Build Module Equivalent**:
```yaml
- type: justfiles
  system:
    - source: justfiles/system.just
      destination: system.just
```

---

## 14. brew (Homebrew)

Aurora includes Homebrew (Linuxbrew) support via the `brew` image.

### Brewfile Collections (Aurora-DX)

| Brewfile | Contents | Keep? |
|----------|----------|-------|
| `ai.Brewfile` | AI/ML tools | ☐ |
| `cli.Brewfile` | CLI utilities | ☐ |
| `fonts.Brewfile` | Developer fonts | ☐ |
| `k8s.Brewfile` | Kubernetes tools | ☐ |

**Blue Build Module Equivalent**:
```yaml
- type: brew
  brewfiles:
    - cli.Brewfile
```

---

## 15. DX (Developer Experience) Variant

**Source**: `build_files/dx/00-dx.sh`

The Aurora-DX variant adds extensive developer tooling.

### DX Fedora Packages

| Package | Description | Keep? |
|---------|-------------|-------|
| `android-tools` | Android ADB/fastboot | ☐ |
| `bcc` | BPF compiler collection | ☐ |
| `bpftop` | BPF program monitor | ☐ |
| `bpftrace` | BPF tracing language | ☐ |
| `cockpit-*` | Web-based system management | ☐ |
| `flatpak-builder` | Flatpak build tool | ☐ |
| `incus` / `lxc` | Container/VM management | ☐ |
| `libvirt` / `qemu` | Full virtualization stack | ☐ |
| `virt-manager` | VM management GUI | ☐ |
| `podman-compose` | Docker Compose for Podman | ☐ |
| `podman-tui` | Podman terminal UI | ☐ |
| `rocm-*` | AMD ROCm compute stack | ☐ |
| `sysprof` | System profiler | ☐ |
| `ydotool` | Wayland automation tool | ☐ |

### DX External Packages

| Source | Package | Description | Keep? |
|--------|---------|-------------|-------|
| Docker repo | `docker-ce`, `docker-buildx-plugin`, `docker-compose-plugin` | Docker container runtime | ☐ |
| Microsoft repo | `code` | Visual Studio Code | ☐ |
| COPR `karmab/kcli` | `kcli` | Virtualization management CLI | ☐ |
| COPR `gmaglione/podman-bootc` | `podman-bootc` | Bootable container support | ☐ |

### DX Enabled Services

| Service | Description | Keep? |
|---------|-------------|-------|
| `docker.socket` | Docker socket activation | ☐ |
| `podman.socket` | Podman socket activation | ☐ |
| `swtpm-workaround.service` | Software TPM workaround | ☐ |

---

## Summary: Key Decisions for Custom Image

### Must Keep (Essential)
- Base ostree/bootc functionality
- Basic filesystem tools
- Core KDE packages

### Recommended Keep (Quality of Life)
- `distrobox` - Container-based development
- `mesa-va-drivers-freeworld` - Video acceleration
- `flatpak-preinstall.service` - Flatpak management
- `fstrim.timer` - SSD maintenance

### Optional (Personal Preference)
- Fish/Zsh shells
- Terminal enhancements (Starship, Nerd Fonts)
- Gaming-related akmods (xone, xpadneo)
- Specialized hardware support

### Consider Removing
- Hardware support you don't need (Framework laptop, Razer)
- Branding elements (replace with your own)
- Aurora-specific CLI tools

---

## Blue Build Recipe Template

Based on this analysis, here's a starting template for recreating Aurora's customizations:

```yaml
name: my-custom-os
description: Custom OS based on Aurora analysis
base-image: ghcr.io/ublue-os/kinoite-main
image-version: latest

modules:
  - type: akmods
    install:
      - v4l2loopback
    # nvidia-driver: nvidia-open  # Uncomment for Nvidia

  - type: rpm-ostree
    install:
      - distrobox
      - fish
      - fastfetch
      - mesa-va-drivers-freeworld
    remove:
      - firefox
      - firefox-langpacks

  - type: default-flatpaks
    system:
      repo-url: https://flathub.org/repo/flathub.flatpakrepo
      repo-name: flathub
      install:
        - io.github.kolunmi.Bazaar

  - type: systemd
    system:
      enabled:
        - fstrim.timer

  - type: files
    files:
      - source: system
        destination: /
```

---

*Document generated from Aurora repository analysis. Last updated: See git history.*
