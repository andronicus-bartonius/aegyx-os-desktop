# Bazzite Additions for Aurora-Based Image

**Features from Bazzite not included in Aurora that may benefit a gaming + engineering workstation**

This document lists packages, drivers, optimizations, and services from Bazzite that are **not present in Aurora** and could be valuable additions for your custom image. Desktop environment configs and branding are excluded.

> **Source Repository**: https://github.com/ublue-os/bazzite

---

## Table of Contents

1. [Custom Kernel](#1-custom-kernel)
2. [Gaming Packages](#2-gaming-packages)
3. [Performance Optimizations](#3-performance-optimizations)
4. [Patched System Components](#4-patched-system-components)
5. [Hardware Support](#5-hardware-support)
6. [System Services](#6-system-services)
7. [Utilities & Tools](#7-utilities--tools)
8. [Android Emulation](#8-android-emulation)
9. [Streaming & Remote Play](#9-streaming--remote-play)
10. [COPR Repositories](#10-copr-repositories)

---

## 1. Custom Kernel

Bazzite uses a **custom gaming-optimized kernel** instead of the standard Fedora kernel.

| Component | Description | Recommendation |
|-----------|-------------|----------------|
| `kernel-bazzite` | Custom kernel with gaming patches, handheld support, fsync/futex2 | ⭐ High - Better gaming performance |

**Source**: `ghcr.io/bazzite-org/kernel-bazzite`

**Benefits**:
- fsync/futex2 support for better Wine/Proton performance
- Additional handheld device drivers
- Steam Deck hardware patches
- Improved scheduler for gaming workloads

**Blue Build Implementation**:
```yaml
# Would require using Bazzite's kernel image as source
# This is complex - consider keeping Aurora's kernel unless gaming is primary use case
```

---

## 2. Gaming Packages

### Core Gaming Stack

| Package | Description | Add? |
|---------|-------------|------|
| `steam` | Steam client (native RPM, not Flatpak) | ☐ |
| `lutris` | Game launcher for non-Steam games | ☐ |
| `gamescope` | Valve's micro-compositor for gaming | ⭐ |
| `gamescope-libs` (x86_64 + i686) | Gamescope libraries (32-bit and 64-bit) | ⭐ |
| `gamescope-shaders` | Custom shaders for Gamescope | ☐ |
| `umu-launcher` | Universal game launcher (Proton outside Steam) | ⭐ |
| `winetricks` | Wine helper scripts (downloaded at build) | ☐ |

### Performance Overlays & Enhancements

| Package | Description | Add? |
|---------|-------------|------|
| `mangohud` (x86_64 + i686) | FPS counter and performance overlay | ⭐ |
| `vkBasalt` (x86_64 + i686) | Vulkan post-processing layer (sharpening, CAS) | ☐ |
| `libobs_vkcapture` (x86_64 + i686) | OBS Vulkan/OpenGL game capture | ☐ |
| `libobs_glcapture` (x86_64 + i686) | OBS OpenGL capture | ☐ |

### VR/XR Support

| Package | Description | Add? |
|---------|-------------|------|
| `openxr` | OpenXR runtime for VR applications | ☐ |

### Controller & Input

| Package | Description | Add? |
|---------|-------------|------|
| `xwiimote-ng` | Wii Remote support | ☐ |
| `ds-inhibit` | Prevents DualShock controllers from going to sleep | ⭐ |
| `SDL_GameControllerDB` | Extended game controller database | ⭐ |

### 32-bit Libraries (for older games)

| Package | Description | Add? |
|---------|-------------|------|
| `libFAudio.i686` | FAudio 32-bit for older Windows games | ☐ |
| `mesa-va-drivers.i686` | 32-bit VA-API drivers | ☐ |
| `libxcrypt-compat` | Legacy crypt library compatibility | ⭐ |

### Media Playback (Blu-ray)

| Package | Description | Add? |
|---------|-------------|------|
| `libaacs` | Blu-ray AACS decryption | ☐ |
| `libbdplus` | Blu-ray BD+ decryption | ☐ |
| `libbluray` | Blu-ray playback library | ☐ |
| `libbluray-utils` | Blu-ray utilities | ☐ |

**Blue Build Implementation**:
```yaml
- type: rpm-ostree
  install:
    - gamescope
    - mangohud
    - libxcrypt-compat
    # Steam/Lutris better as Flatpaks for isolation
```

---

## 3. Performance Optimizations

### CPU Scheduler (sched-ext)

| Package | Description | Add? |
|---------|-------------|------|
| `scx-scheds` | Pluggable CPU scheduler framework | ⭐⭐ |
| `scx-tools` | sched-ext scheduler tools | ⭐⭐ |

**Benefits**:
- Improved responsiveness under load
- Better gaming performance with `scx_lavd` or `scx_rusty` schedulers
- Can significantly reduce input latency

**Source**: COPR `bieszczaders/kernel-cachyos-addons`

### Memory & Storage

| Package | Description | Add? |
|---------|-------------|------|
| `bees` | Btrfs deduplication daemon | ☐ |
| `snapper` | Btrfs snapshot management | ⭐ |
| `btrfs-assistant` | GUI for Btrfs/Snapper management | ⭐ |
| `compsize` | Btrfs compression statistics | ☐ |

### Power Management

| Package | Description | Add? |
|---------|-------------|------|
| `ryzenadj` | AMD Ryzen power management tool | ☐ |
| `tuned` profiles | Custom Bazzite tuned profiles for gaming | ⭐ |

### Huge Pages

| Service | Description | Add? |
|---------|-------------|------|
| `dev-hugepages1G.mount` | 1GB huge pages for gaming performance | ⭐ |

**Blue Build Implementation**:
```yaml
- type: rpm-ostree
  install:
    - scx-scheds
    - scx-tools
    - snapper
    - btrfs-assistant

- type: systemd
  system:
    enabled:
      - dev-hugepages1G.mount
```

---

## 4. Patched System Components

Bazzite uses **Valve's patched versions** of critical system components for better gaming compatibility.

| Component | Patches | Add? |
|-----------|---------|------|
| `pipewire` | Valve's Steam Deck patches | ⭐⭐ |
| `wireplumber` | Valve's patches | ⭐⭐ |
| `bluez` | Improved Bluetooth latency | ⭐ |
| `mesa` | Terra's Mesa with latest features | ⭐⭐ |
| `xorg-x11-server-Xwayland` | Valve's Xwayland patches | ⭐ |
| `NetworkManager` | Bazzite multilib patches | ☐ |
| `fwupd` | Patched firmware updater | ☐ |

**Source**: 
- COPR `ublue-os/bazzite-multilib`
- Terra repository (`terra-mesa`)

**Implementation Note**: These require swapping packages from specific repos with version locks. Complex to implement but provides significant gaming benefits.

---

## 5. Hardware Support

### Surface Device Support

| Package | Description | Add? |
|---------|-------------|------|
| `iptsd` | Intel Precision Touch support | ☐ |
| `libwacom-surface` | Surface pen support | ☐ |

**Source**: `linux-surface` repository

### Framework Laptop

| Package | Description | Add? |
|---------|-------------|------|
| `fw-ectool` | Framework EC tool | ☐ |
| `fw-fanctrl` | Framework fan control | ☐ |
| `framework-system` | Framework system utilities | ☐ |

### AMD Hardware

| Package | Description | Add? |
|---------|-------------|------|
| `ryzenadj` | AMD Ryzen CPU/APU tuning | ☐ |
| `rocm-clinfo` | ROCm OpenCL info tool | ☐ |

### Input Devices

| Package | Description | Add? |
|---------|-------------|------|
| `libinput-utils` | libinput debugging utilities | ☐ |
| `i2c-tools` | I2C device utilities | ☐ |
| `libcec` | HDMI-CEC control library | ⭐ |

### Handheld Gaming Devices

| Package | Description | Add? |
|---------|-------------|------|
| `hhd` | Handheld Daemon (ROG Ally, Legion Go, etc.) | ☐ |
| `hhd-ui` | Handheld Daemon GUI | ☐ |
| `sdgyrodsu` | Gyro to DSU for Steam Input | ☐ |

**Note**: Only relevant if targeting handheld devices.

---

## 6. System Services

### Gaming-Specific Services

| Service | Description | Add? |
|---------|-------------|------|
| `ds-inhibit.service` | Prevents DualShock sleep during gaming | ⭐ |
| `dev-hugepages1G.mount` | Large memory pages for games | ⭐ |
| `bazzite-hardware-setup.service` | Hardware detection and optimization | ☐ |

### HDMI-CEC Services (TV/Projector Control)

| Service | Description | Add? |
|---------|-------------|------|
| `cec-onboot.service` | Turn on TV/display via CEC on boot | ☐ |
| `cec-onpoweroff.service` | Turn off TV via CEC on shutdown | ☐ |
| `cec-onsleep.service` | CEC commands on sleep | ☐ |

### System Health

| Package | Description | Add? |
|---------|-------------|------|
| `greenboot` | Health checking for ostree systems | ⭐⭐ |
| `greenboot-default-health-checks` | Default health checks | ⭐⭐ |

**Benefits**: Auto-rollback if system fails to boot properly.

**Blue Build Implementation**:
```yaml
- type: rpm-ostree
  install:
    - greenboot
    - greenboot-default-health-checks

- type: systemd
  system:
    enabled:
      - greenboot-healthcheck.service
      - greenboot-set-rollback-trigger.service
```

---

## 7. Utilities & Tools

### Terminal & CLI

| Package | Description | Add? |
|---------|-------------|------|
| `glow` | Terminal Markdown renderer | ⭐ |
| `gum` | Shell script UI toolkit | ⭐ |
| `duf` | Modern `df` replacement | ☐ |
| `vim` | Text editor | ☐ |
| `f3` | Flash drive/SD card tester | ☐ |

### Archive Formats

| Package | Description | Add? |
|---------|-------------|------|
| `rar` | RAR archive support (negativo17) | ☐ |
| `lzip` | LZIP compression | ☐ |

### System Tools

| Package | Description | Add? |
|---------|-------------|------|
| `lshw` | Hardware lister | ⭐ |
| `ls-iommu` | IOMMU group viewer (for GPU passthrough) | ☐ |
| `xdotool` | X11 automation tool | ☐ |
| `wmctrl` | Window manager control | ☐ |
| `xwininfo` | X window information | ☐ |
| `yad` | Dialog boxes for scripts | ☐ |
| `pulseaudio-utils` | PulseAudio CLI tools (pactl, etc.) | ⭐ |
| `ScopeBuddy` | Input scope monitoring | ☐ |

### Network

| Package | Description | Add? |
|---------|-------------|------|
| `iwd` | Intel Wireless Daemon (optional WiFi backend) | ☐ |
| `usbip` | USB over IP | ☐ |

### Python

| Package | Description | Add? |
|---------|-------------|------|
| `python3-pip` | Python package installer | ⭐ |
| `python3-icoextract` | Windows icon extractor | ☐ |

### Fonts

| Package | Description | Add? |
|---------|-------------|------|
| `nerd-fonts` | Complete Nerd Fonts package | ⭐ |
| `twitter-twemoji-fonts` | Twitter emoji font | ☐ |
| `google-noto-sans-cjk-fonts` | CJK (Chinese/Japanese/Korean) fonts | ⭐ |
| `lato-fonts` | Lato font family | ☐ |
| `fira-code-fonts` | Fira Code programming font | ⭐ |

---

## 8. Android Emulation

| Package | Description | Add? |
|---------|-------------|------|
| `waydroid` | Android container for Linux | ☐ |
| `cage` | Wayland kiosk compositor (for Waydroid) | ☐ |
| `wlr-randr` | Wayland output management | ☐ |

**Use Case**: Run Android apps/games natively on Linux.

**Blue Build Implementation**:
```yaml
- type: rpm-ostree
  install:
    - waydroid
    - cage
    - wlr-randr

- type: systemd
  system:
    disabled:
      - waydroid-container.service  # Enable on-demand
```

---

## 9. Streaming & Remote Play

| Package | Description | Add? |
|---------|-------------|------|
| `Sunshine` | Self-hosted game streaming server | ⭐⭐ |

**Benefits**:
- Stream games to Moonlight clients (phones, tablets, other PCs)
- Works with any GPU (not just Nvidia)
- Low latency streaming

**Source**: COPR `lizardbyte/beta`

**Blue Build Implementation**:
```yaml
- type: rpm-ostree
  repos:
    - copr:lizardbyte/beta
  install:
    - Sunshine

- type: systemd
  user:
    disabled:
      - sunshine.service  # User enables manually
```

---

## 10. COPR Repositories

Bazzite enables several COPR repositories for gaming packages.

| COPR | Purpose | Packages |
|------|---------|----------|
| `ublue-os/bazzite` | Bazzite-specific packages | gamescope-session, steamdeck-* |
| `ublue-os/bazzite-multilib` | Patched 32-bit libs | pipewire, mesa, bluez |
| `ublue-os/obs-vkcapture` | OBS game capture | libobs_vkcapture |
| `ublue-os/hhd` | Handheld daemon | hhd, hhd-ui |
| `ycollet/audinux` | Audio production tools | - |
| `ublue-os/rom-properties` | ROM file thumbnails | rom-properties-kf6 |
| `lizardbyte/beta` | Game streaming | Sunshine |
| `che/nerd-fonts` | Nerd Fonts | nerd-fonts |
| `bieszczaders/kernel-cachyos-addons` | CPU scheduler | scx-scheds, scx-tools |

### External Repositories

| Repository | Purpose |
|------------|---------|
| `terra` | Latest Mesa, modern packages |
| `terra-mesa` | Bleeding-edge Mesa drivers |
| `negativo17-fedora-steam` | Steam native RPM |
| `negativo17-fedora-rar` | RAR support |

---

## Priority Recommendations

### ⭐⭐ High Priority (Significant Gaming/Dev Benefits)

1. **`scx-scheds` + `scx-tools`** - Dramatically improves system responsiveness
2. **`greenboot`** - Safety net for system updates
3. **`Sunshine`** - Game streaming to any device
4. **Valve's patched Mesa/PipeWire** - Better gaming compatibility

### ⭐ Medium Priority (Nice to Have)

1. **`gamescope`** - Essential for some games, HDR support
2. **`mangohud`** - Performance monitoring
3. **`snapper` + `btrfs-assistant`** - Easy system snapshots
4. **`ds-inhibit`** - PlayStation controller support
5. **`nerd-fonts`** - Developer fonts
6. **`glow` + `gum`** - Modern CLI tools

### Optional (Specific Use Cases)

- **Waydroid** - If you need Android apps
- **Handheld packages** - Only for handheld devices
- **Surface packages** - Only for Surface devices
- **VR packages** - Only if doing VR development/gaming

---

## Blue Build Recipe Snippet

```yaml
# Recommended Bazzite additions for gaming+dev workstation
modules:
  - type: rpm-ostree
    repos:
      - copr:bieszczaders/kernel-cachyos-addons
      - copr:lizardbyte/beta
      - copr:che/nerd-fonts
    install:
      # Performance
      - scx-scheds
      - scx-tools
      - greenboot
      - greenboot-default-health-checks
      
      # Gaming
      - gamescope
      - mangohud
      - libxcrypt-compat
      
      # Streaming
      - Sunshine
      
      # Utilities
      - glow
      - gum
      - lshw
      - python3-pip
      - pulseaudio-utils
      
      # Fonts
      - nerd-fonts
      - google-noto-sans-cjk-fonts
      - fira-code-fonts
      
      # Btrfs Management
      - snapper
      - btrfs-assistant

  - type: systemd
    system:
      enabled:
        - greenboot-healthcheck.service
        - greenboot-set-rollback-trigger.service
        - dev-hugepages1G.mount
```

---

*Document generated from Bazzite repository analysis. Features listed are additions beyond what Aurora already provides.*
