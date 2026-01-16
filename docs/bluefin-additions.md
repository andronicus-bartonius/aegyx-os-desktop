# Bluefin Additions for Aurora-Based Image

**Features from Bluefin not included in Aurora that may benefit a productivity + engineering workstation**

This document lists packages, tools, and configurations from Bluefin that are **not present in Aurora** and could be valuable additions for your custom image. GNOME-specific packages and branding are excluded since Bluefin is GNOME-based while Aurora is KDE-based.

> **Source Repository**: https://github.com/ublue-os/bluefin

---

## Table of Contents

1. [Enterprise & Authentication](#1-enterprise--authentication)
2. [Backup & Encryption](#2-backup--encryption)
3. [Development Tools](#3-development-tools)
4. [Printing & Peripherals](#4-printing--peripherals)
5. [Power Management](#5-power-management)
6. [Remote Access](#6-remote-access)
7. [DX (Developer Experience) Packages](#7-dx-developer-experience-packages)
8. [Services Configuration](#8-services-configuration)

---

## 1. Enterprise & Authentication

Bluefin includes packages for enterprise/corporate environment integration that Aurora lacks.

### Active Directory / Kerberos Integration

| Package | Description | Add? |
|---------|-------------|------|
| `adcli` | Active Directory enrollment utility | ☐ |
| `krb5-workstation` | Kerberos 5 client tools | ☐ |
| `sssd-ad` | System Security Services Daemon - AD provider | ☐ |
| `sssd-krb5` | SSSD Kerberos authentication | ☐ |
| `sssd-nfs-idmap` | SSSD NFS ID mapping | ☐ |
| `libsss_autofs` | SSSD autofs integration | ☐ |
| `oddjob-mkhomedir` | Auto-create home directories on login | ☐ |

### Samba/Windows Integration

| Package | Description | Add? |
|---------|-------------|------|
| `samba-dcerpc` | Samba DCE/RPC libraries | ☐ |
| `samba-ldb-ldap-modules` | Samba LDB LDAP modules | ☐ |
| `samba-winbind-clients` | Winbind client tools | ☐ |
| `samba-winbind-modules` | Winbind PAM/NSS modules | ☐ |

**Use Case**: Corporate environments with Active Directory, shared Windows drives, or Kerberos authentication.

**Blue Build Implementation**:
```yaml
- type: rpm-ostree
  install:
    - adcli
    - krb5-workstation
    - sssd-ad
    - sssd-krb5
    - oddjob-mkhomedir
    - samba-winbind-clients
    - samba-winbind-modules
```

---

## 2. Backup & Encryption

### Backup Tools

| Package | Description | Add? |
|---------|-------------|------|
| `borgbackup` | Deduplicating backup program | ⭐⭐ |
| `restic` | Fast, secure backup program | ⭐ |

**Note**: Aurora includes `restic` but **not** `borgbackup`.

### Encrypted Filesystems

| Package | Description | Add? |
|---------|-------------|------|
| `cryfs` | Encrypted filesystem for cloud storage | ⭐ |
| `fuse-encfs` | Encrypted pass-through filesystem | ☐ |

**Use Case**: Secure backup of sensitive data, encrypted Dropbox/cloud folders.

**Blue Build Implementation**:
```yaml
- type: rpm-ostree
  install:
    - borgbackup
    - cryfs
```

---

## 3. Development Tools

### Build Essentials

| Package | Description | Add? |
|---------|-------------|------|
| `gcc` | GNU C Compiler | ⭐⭐ |
| `make` | GNU Make build tool | ⭐⭐ |
| `python3-pygit2` | Python bindings for libgit2 | ☐ |

**Note**: Aurora-DX includes these, but base Aurora does not. If you want development tools in base image, add these.

### Git Extensions

| Package | Description | Add? |
|---------|-------------|------|
| `git-subtree` | Git subtree merge strategy | ⭐ |
| `git-svn` | Git-SVN interoperability | ☐ |

### Database

| Package | Description | Add? |
|---------|-------------|------|
| `libgda` | GNOME Data Access library | ☐ |
| `libgda-sqlite` | SQLite provider for libgda | ☐ |

### Graphics/OpenGL

| Package | Description | Add? |
|---------|-------------|------|
| `mesa-libGLU` | OpenGL Utility Library | ⭐ |
| `igt-gpu-tools` | Intel GPU debugging tools | ☐ |

**Blue Build Implementation**:
```yaml
- type: rpm-ostree
  install:
    - gcc
    - make
    - git-subtree
    - mesa-libGLU
```

---

## 4. Printing & Peripherals

### Printer Drivers

| Package | Description | Add? |
|---------|-------------|------|
| `hplip` | HP Linux Imaging and Printing | ⭐ |
| `printer-driver-brlaser` | Brother laser printer driver | ☐ |
| `foo2zjs` | Printer driver for ZjStream protocol | ☐ |

### Apple Device Support

| Package | Description | Add? |
|---------|-------------|------|
| `ifuse` | Mount iOS devices as filesystem | ⭐ |
| `libimobiledevice` | iOS device communication library | ⭐ |
| `usbmuxd` | USB multiplexor daemon for iOS | ⭐ |

**Use Case**: If you use iPhones/iPads and need file access or backup.

### Input Devices

| Package | Description | Add? |
|---------|-------------|------|
| `libratbag-ratbagd` | Gaming mouse configuration daemon | ⭐ |

**Note**: Works with Piper GUI for configuring Logitech, Razer, and other gaming mice.

**Blue Build Implementation**:
```yaml
- type: rpm-ostree
  install:
    - hplip
    - ifuse
    - libimobiledevice
    - usbmuxd
    - libratbag-ratbagd
```

---

## 5. Power Management

| Package | Description | Add? |
|---------|-------------|------|
| `powertop` | Power consumption analyzer | ⭐⭐ |
| `powerstat` | Power consumption statistics | ☐ |
| `switcheroo-control` | Dual-GPU switching control | ⭐ |

**Use Case**: Laptop users wanting to optimize battery life and manage hybrid graphics.

**Blue Build Implementation**:
```yaml
- type: rpm-ostree
  install:
    - powertop
    - switcheroo-control
```

---

## 6. Remote Access

| Package | Description | Add? |
|---------|-------------|------|
| `waypipe` | Wayland over SSH (remote Wayland apps) | ⭐⭐ |
| `openssh-askpass` | SSH password prompt dialog | ⭐ |

**Use Case**: Run graphical Wayland applications over SSH connections.

**Blue Build Implementation**:
```yaml
- type: rpm-ostree
  install:
    - waypipe
    - openssh-askpass
```

---

## 7. DX (Developer Experience) Packages

These are in Bluefin-DX but **not** in Aurora-DX. Consider adding if you want a more complete development environment.

### Profiling & Debugging

| Package | Description | Add? |
|---------|-------------|------|
| `tiptop` | Performance monitoring for CPU | ☐ |
| `genisoimage` | Create ISO images | ☐ |
| `util-linux-script` | Script command for recording terminal | ☐ |

### Fonts (Developer)

| Package | Description | Add? |
|---------|-------------|------|
| `cascadia-code-fonts` | Microsoft's Cascadia Code font | ⭐ |
| `jetbrains-mono-fonts-all` | JetBrains Mono font family | ⭐ |
| `opendyslexic-fonts` | Dyslexia-friendly font | ☐ |

**Note**: Aurora-DX has Nerd Fonts but not these specific developer fonts.

### Input Methods

| Package | Description | Add? |
|---------|-------------|------|
| `mozc` | Japanese input method | ☐ |
| `ibus-mozc` | IBus integration for Mozc | ☐ |

---

## 8. Services Configuration

### Services Enabled in Bluefin (not in Aurora)

| Service | Description | Add? |
|---------|-------------|------|
| `bazaar.service` (user) | Flatpak app browser service | ☐ |
| `podman-auto-update.timer` (user) | Auto-update Podman containers | ⭐ |
| `rpm-ostree-countme.service` | Fedora usage statistics | ☐ |

**Note**: Aurora explicitly disables `rpm-ostree-countme` for privacy. Bluefin enables it. Your choice based on privacy preference.

**Blue Build Implementation**:
```yaml
- type: systemd
  user:
    enabled:
      - podman-auto-update.timer
```

---

## Priority Recommendations

### ⭐⭐ High Priority (Significant Productivity Benefits)

1. **`borgbackup`** - Industry-standard deduplicating backup
2. **`gcc` + `make`** - Essential for compiling software (if not using DX variant)
3. **`powertop`** - Essential for laptop users
4. **`waypipe`** - Remote Wayland application support

### ⭐ Medium Priority (Nice to Have)

1. **`cryfs`** - Encrypted cloud storage folders
2. **`hplip`** - HP printer support
3. **`ifuse` + `libimobiledevice`** - iOS device support
4. **`libratbag-ratbagd`** - Gaming mouse configuration
5. **`switcheroo-control`** - Hybrid graphics switching
6. **`cascadia-code-fonts`** - Popular developer font
7. **`jetbrains-mono-fonts-all`** - JetBrains developer font
8. **`mesa-libGLU`** - OpenGL compatibility
9. **`openssh-askpass`** - SSH GUI prompts
10. **`git-subtree`** - Advanced Git workflows

### Optional (Specific Use Cases)

- **Enterprise packages** - Only for corporate/AD environments
- **Input methods** - Only if typing in Japanese/CJK languages
- **Brother printer driver** - Only if you have Brother printers

---

## Comparison: Bluefin vs Aurora Base Packages

| Category | Aurora Has | Bluefin Adds |
|----------|------------|--------------|
| Backup | restic, rclone | borgbackup |
| Encryption | clevis, LUKS tools | cryfs, encfs |
| Build tools | (in DX only) | gcc, make in base |
| iOS support | ❌ | ifuse, libimobiledevice |
| HP printing | ❌ | hplip |
| Power tools | thermald | powertop, powerstat |
| Remote Wayland | ❌ | waypipe |
| Gaming mice | ❌ | libratbag-ratbagd |
| Hybrid GPU | ❌ | switcheroo-control |
| Enterprise/AD | ❌ | Full AD/Kerberos stack |

---

## Blue Build Recipe Snippet

```yaml
# Recommended Bluefin additions for productivity workstation
modules:
  - type: rpm-ostree
    install:
      # Backup & Security
      - borgbackup
      - cryfs
      
      # Development (if not using DX variant)
      - gcc
      - make
      - git-subtree
      - mesa-libGLU
      
      # Power Management (laptops)
      - powertop
      - switcheroo-control
      
      # Peripherals
      - hplip
      - ifuse
      - libimobiledevice
      - usbmuxd
      - libratbag-ratbagd
      
      # Remote Access
      - waypipe
      - openssh-askpass
      
      # Fonts
      - cascadia-code-fonts
      - jetbrains-mono-fonts-all

  - type: systemd
    user:
      enabled:
        - podman-auto-update.timer
```

---

## Combined Additions: Bazzite + Bluefin

For a complete gaming + development workstation, consider combining the best of both:

```yaml
# Best of Bazzite (Gaming)
- scx-scheds, scx-tools     # CPU scheduler
- greenboot                  # System health
- gamescope, mangohud       # Gaming tools
- Sunshine                   # Game streaming
- snapper, btrfs-assistant  # Btrfs snapshots

# Best of Bluefin (Productivity)
- borgbackup                 # Backup
- powertop                   # Power management
- waypipe                    # Remote Wayland
- ifuse, libimobiledevice   # iOS support
- libratbag-ratbagd         # Gaming mice
- cascadia-code-fonts       # Developer fonts
```

---

*Document generated from Bluefin repository analysis. Features listed are additions beyond what Aurora already provides.*
