# Aegyx OS Build Todo List

**Customizations to add to Aurora-based image**

This document tracks planned additions from Bazzite and Bluefin that will enhance the Aegyx OS image for gaming and engineering workloads.

---

## Pre-Flight Check: Already in Aurora

The following requested packages are **already included** in Aurora and do not need to be added:

| Package | Status | Location in Aurora |
|---------|--------|-------------------|
| `borgbackup` | ✅ Included | `build_files/base/04-packages.sh` |
| `powertop` | ✅ Included | `build_files/base/04-packages.sh` |

---

## Part 1: Easy Additions

These packages can be added with minimal effort. Standard Fedora packages or well-maintained COPRs.

### Performance & System Health

| Package | Source | Description | Status |
|---------|--------|-------------|--------|
| `scx-scheds` | COPR `bieszczaders/kernel-cachyos-addons` | Pluggable CPU schedulers for better responsiveness | ☐ Todo |
| `scx-tools` | COPR `bieszczaders/kernel-cachyos-addons` | sched-ext scheduler management tools | ☐ Todo |
| `greenboot` | Fedora | System health monitoring with auto-rollback | ☐ Todo |
| `greenboot-default-health-checks` | Fedora | Default health check scripts for greenboot | ☐ Todo |

**Implementation:**
```yaml
- type: rpm-ostree
  repos:
    - copr:bieszczaders/kernel-cachyos-addons
  install:
    - scx-scheds
    - scx-tools
    - greenboot
    - greenboot-default-health-checks

- type: systemd
  system:
    enabled:
      - greenboot-healthcheck.service
      - greenboot-set-rollback-trigger.service
```

---

### Btrfs Snapshot Management

| Package | Source | Description | Status |
|---------|--------|-------------|--------|
| `snapper` | Fedora | Btrfs snapshot manager CLI | ☐ Todo |
| `btrfs-assistant` | Fedora | GUI for Btrfs/Snapper management | ☐ Todo |

**Implementation:**
```yaml
- type: rpm-ostree
  install:
    - snapper
    - btrfs-assistant
```

---

### Gaming & Controller Support

| Package | Source | Description | Status |
|---------|--------|-------------|--------|
| `ds-inhibit` | COPR `ublue-os/bazzite` | Prevents DualShock controllers from sleeping | ☐ Todo |

**Implementation:**
```yaml
- type: rpm-ostree
  repos:
    - copr:ublue-os/bazzite
  install:
    - ds-inhibit

- type: systemd
  system:
    enabled:
      - ds-inhibit.service
```

---

### RGB Lighting Control

| Package | Source | Description | Status |
|---------|--------|-------------|--------|
| `openrgb` | Fedora | Open source RGB lighting control for peripherals | ☐ Todo |

**Implementation:**
```yaml
- type: rpm-ostree
  install:
    - openrgb
```

---

### Security & Encryption

| Package | Source | Description | Status |
|---------|--------|-------------|--------|
| `cryfs` | Fedora | Encrypted filesystem for cloud storage folders | ☐ Todo |

**Implementation:**
```yaml
- type: rpm-ostree
  install:
    - cryfs
```

---

### Remote Access

| Package | Source | Description | Status |
|---------|--------|-------------|--------|
| `waypipe` | Fedora | Run Wayland apps over SSH (remote GUI apps) | ☐ Todo |

**Implementation:**
```yaml
- type: rpm-ostree
  install:
    - waypipe
```

---

### Printing

| Package | Source | Description | Status |
|---------|--------|-------------|--------|
| `hplip` | Fedora | HP Linux Imaging and Printing (HP printer drivers) | ☐ Todo |

**Implementation:**
```yaml
- type: rpm-ostree
  install:
    - hplip
```

---

## Part 1 Combined Recipe

Copy this to your Blue Build recipe for all easy additions:

```yaml
modules:
  - type: rpm-ostree
    repos:
      - copr:bieszczaders/kernel-cachyos-addons
      - copr:ublue-os/bazzite
    install:
      # Performance & System Health
      - scx-scheds
      - scx-tools
      - greenboot
      - greenboot-default-health-checks
      
      # Btrfs Snapshots
      - snapper
      - btrfs-assistant
      
      # Gaming
      - ds-inhibit
      
      # RGB Control
      - openrgb
      
      # Security
      - cryfs
      
      # Remote Access
      - waypipe
      
      # Printing
      - hplip
      
      # Fonts
      - cascadia-code-fonts
      - jetbrains-mono-fonts-all

  - type: systemd
    system:
      enabled:
        - greenboot-healthcheck.service
        - greenboot-set-rollback-trigger.service
        - ds-inhibit.service
```

---

## Part 2: Moderate Complexity Additions

These require additional research or have dependencies that may need attention.

### Gamescope

| Package | Source | Description | Status |
|---------|--------|-------------|--------|
| `gamescope` | Fedora / Terra | Valve's micro-compositor for gaming | ☐ Todo |

**What it does:**
- Provides HDR support
- Enables resolution scaling and frame limiting
- Fixes compatibility issues with some games
- Creates isolated gaming environment

**Complexity factors:**
- Basic package installs easily from Fedora
- **However**: Full HDR and advanced features require Valve's patched Mesa (see Part 3)
- Without patched Mesa, gamescope works but with limited functionality

**Research needed:**
- [ ] Test if Fedora's gamescope works adequately without Valve's Mesa patches
- [ ] Determine which specific features require patched Mesa
- [ ] Evaluate if basic gamescope is sufficient for your use case

**Basic Implementation (limited features):**
```yaml
- type: rpm-ostree
  install:
    - gamescope
```

**Full Implementation (requires Part 3):**
See Valve's Patched Stack below.

---

## Part 3: Complex Additions (Requires Significant Research)

These additions involve package swapping, version locking, and potential system instability. **Do not attempt without thorough testing.**

### Valve's Patched Mesa/PipeWire/Bluez Stack

| Component | Source | Description | Status |
|-----------|--------|-------------|--------|
| `mesa` (patched) | Terra / `ublue-os/bazzite-multilib` | Valve's Mesa with gaming optimizations | ☐ Research |
| `pipewire` (patched) | `ublue-os/bazzite-multilib` | Steam Deck audio patches | ☐ Research |
| `wireplumber` (patched) | `ublue-os/bazzite` | Session manager patches | ☐ Research |
| `bluez` (patched) | `ublue-os/bazzite-multilib` | Lower Bluetooth latency for controllers | ☐ Research |
| `xorg-x11-server-Xwayland` (patched) | `ublue-os/bazzite-multilib` | Valve's Xwayland improvements | ☐ Research |

**What it provides:**
- Better HDR support in gamescope
- Improved Bluetooth controller latency
- Enhanced audio handling for games
- Better Wine/Proton compatibility
- Steam Deck-level gaming experience

**Complexity factors:**
1. **Package swapping**: Must replace Fedora's versions with Bazzite's patched versions
2. **Version locks**: Must lock versions to prevent updates breaking the stack
3. **32-bit libraries**: Gaming requires both x86_64 and i686 versions
4. **Multiple repos**: Requires Terra, bazzite-multilib, and specific priority settings
5. **Risk**: Incorrect implementation can break graphics, audio, or Bluetooth

**Research tasks:**
- [ ] Study Bazzite's Containerfile implementation (lines 119-178)
- [ ] Understand repo priority settings needed
- [ ] Document exact package swap commands
- [ ] Create test environment before applying to main build
- [ ] Develop rollback procedure if implementation fails

**Reference implementation from Bazzite:**
```bash
# From Bazzite Containerfile - DO NOT COPY DIRECTLY
# This is for reference only - requires adaptation

declare -A toswap=(
    ["copr:copr.fedorainfracloud.org:ublue-os:bazzite"]="wireplumber"
    ["copr:copr.fedorainfracloud.org:ublue-os:bazzite-multilib"]="pipewire bluez xorg-x11-server-Xwayland NetworkManager"
    ["terra-mesa"]="mesa-filesystem"
)

for repo in "${!toswap[@]}"; do
    for package in ${toswap[$repo]}; do
        dnf5 -y swap --repo=$repo $package $package
    done
done

# Version locks to prevent breakage
dnf5 versionlock add \
    pipewire pipewire-alsa pipewire-libs ... \
    wireplumber wireplumber-libs \
    bluez bluez-libs bluez-obexd \
    mesa-dri-drivers mesa-filesystem mesa-libEGL ...
```

**Recommended approach:**
1. Start with Part 1 (easy additions) only
2. Test basic gamescope from Fedora
3. If gaming experience is inadequate, then research Valve's stack
4. Create separate test branch before implementing
5. Consider if the complexity is worth the benefit for your use case

---

## Part 4: Aesthetic Customization & Branding

These files and configurations control the "out of the box" look and feel. All are implemented via Blue Build's `files` module by copying files to the appropriate system paths.

### Directory Structure in aegyx-os-desktop

Create this structure in your Blue Build repo:

```
aegyx-os-desktop/
├── files/
│   └── usr/
│       └── share/
│           ├── backgrounds/           # Wallpapers
│           │   └── aegyx/
│           │       └── wallpaper-1/
│           │           └── contents/
│           │               └── images/
│           │                   └── 3840x2160.jxl
│           ├── wallpapers/            # KDE wallpaper metadata
│           │   └── aegyx-wallpaper-1/
│           │       ├── metadata.json
│           │       └── contents/
│           │           └── images/
│           │               └── 3840x2160.jxl
│           ├── icons/
│           │   └── hicolor/
│           │       └── scalable/
│           │           ├── apps/
│           │           │   └── start-here.svg
│           │           └── places/
│           │               └── distributor-logo.svg
│           ├── pixmaps/
│           │   ├── aegyx-banner.svg
│           │   └── system-logo.png
│           ├── plasma/
│           │   └── look-and-feel/
│           │       └── dev.aegyx.desktop/
│           │           ├── metadata.json
│           │           └── contents/
│           │               ├── defaults
│           │               ├── splash/
│           │               │   └── images/
│           │               │       └── aegyx_logo.svgz
│           │               └── previews/
│           │                   └── splash.png
│           ├── sddm/
│           │   └── themes/
│           │       └── 01-breeze-aegyx/
│           │           ├── theme.conf
│           │           ├── default-logo.svg
│           │           └── background.jxl
│           ├── plymouth/
│           │   └── themes/
│           │       └── spinner/
│           │           └── watermark.png
│           ├── flatpak/
│           │   └── preinstall.d/
│           │       └── aegyx.preinstall
│           └── ublue-os/
│               └── just/
│                   └── 60-aegyx.just
├── scripts/
│   ├── branding.sh
│   └── register-justfiles.sh
└── recipes/
    └── aegyx.yml
```

---

### 4.1 Wallpapers

| File/Directory | Purpose | Status |
|----------------|---------|--------|
| `files/usr/share/backgrounds/aegyx/` | Custom wallpaper images | ☐ Todo |
| `files/usr/share/wallpapers/aegyx-*/` | KDE wallpaper packages with metadata | ☐ Todo |
| `files/usr/share/backgrounds/default.jxl` | Symlink to default wallpaper | ☐ Todo |
| `files/usr/share/backgrounds/default.xml` | Wallpaper slideshow config | ☐ Todo |

**Implementation**: `files` module copies wallpapers, `script` module creates symlinks

---

### 4.2 Logos & Icons

| File | Purpose | Status |
|------|---------|--------|
| `files/usr/share/icons/hicolor/scalable/places/distributor-logo.svg` | Main distro logo (SVG) | ☐ Todo |
| `files/usr/share/icons/hicolor/scalable/places/distributor-logo-symbolic.svg` | Monochrome logo variant | ☐ Todo |
| `files/usr/share/icons/hicolor/scalable/apps/start-here.svg` | App menu icon | ☐ Todo |
| `files/usr/share/pixmaps/aegyx-banner.svg` | Banner logo for about dialogs | ☐ Todo |
| `files/usr/share/pixmaps/system-logo.png` | PNG logo (256x256) | ☐ Todo |
| `files/usr/share/pixmaps/fedora-logo.png` | Compatibility alias (symlink) | ☐ Todo |

**Implementation**: `files` module + `script` for generating PNG variants with ImageMagick

---

### 4.3 KDE Plasma Look-and-Feel

| File/Directory | Purpose | Status |
|----------------|---------|--------|
| `files/usr/share/plasma/look-and-feel/dev.aegyx.desktop/` | Complete look-and-feel package | ☐ Todo |
| `contents/defaults` | Default theme settings (colors, icons, cursor) | ☐ Todo |
| `contents/splash/` | Boot splash animation/logo | ☐ Todo |
| `metadata.json` | Theme metadata | ☐ Todo |

**Note**: Create your own look-and-feel ID (e.g., `dev.aegyx.desktop`) to avoid conflicts.

---

### 4.4 SDDM Login Theme

| File | Purpose | Status |
|------|---------|--------|
| `files/usr/share/sddm/themes/01-breeze-aegyx/theme.conf` | SDDM theme configuration | ☐ Todo |
| `files/usr/share/sddm/themes/01-breeze-aegyx/default-logo.svg` | Login screen logo | ☐ Todo |
| `files/usr/share/sddm/themes/01-breeze-aegyx/background.jxl` | Login screen background | ☐ Todo |

**Implementation**: `files` module

---

### 4.5 Plymouth Boot Splash

| File | Purpose | Status |
|------|---------|--------|
| `files/usr/share/plymouth/themes/spinner/watermark.png` | Boot splash logo | ☐ Todo |

**Implementation**: `files` module

---

### 4.6 Taskbar Default Applications

Aurora modifies the default pinned taskbar apps via sed in a script:

| Target File | Purpose | Status |
|-------------|---------|--------|
| `/usr/share/plasma/plasmoids/org.kde.plasma.taskmanager/contents/config/main.xml` | Default pinned apps | ☐ Todo |

**Implementation**: `script` module with sed command

**Example script** (`scripts/branding.sh`):
```bash
#!/bin/bash
# Set default pinned taskbar applications
sed -i '/<entry name="launchers" type="StringList">/,/<\/entry>/ s/<default>[^<]*<\/default>/<default>preferred:\/\/browser,applications:org.kde.konsole.desktop,applications:org.kde.dolphin.desktop,applications:com.github.AegxyStudio.AppName.desktop<\/default>/' \
  /usr/share/plasma/plasmoids/org.kde.plasma.taskmanager/contents/config/main.xml
```

---

### 4.7 os-release Branding

| File | Purpose | Status |
|------|---------|--------|
| `/usr/lib/os-release` (modified via script) | OS name, version, URLs | ☐ Todo |

**Implementation**: `script` module modifying os-release fields

---

## Part 5: Flatpak Applications

Blue Build uses the `default-flatpaks` module to manage Flatpak applications. This runs on first boot and can install/remove Flatpaks.

### How It Works

1. **System scope**: Apps available to all users, installed to `/var/lib/flatpak`
2. **User scope**: Per-user apps, installed to `~/.local/share/flatpak`
3. **Flathub**: Default remote (Fedora's remote is removed by Aurora)

### Flatpak Recipe Configuration

Add to your Blue Build recipe:

```yaml
- type: default-flatpaks
  notify: true
  system:
    repo-url: https://dl.flathub.org/repo/flathub.flatpakrepo
    repo-name: flathub
    repo-title: Flathub
    install:
      # Browsers
      - org.mozilla.firefox
      # Productivity
      - org.libreoffice.LibreOffice
      - md.obsidian.Obsidian
      # Media
      - org.videolan.VLC
      - com.spotify.Client
      # Development
      - com.visualstudio.code
      - com.jetbrains.IntelliJ-IDEA-Community
      # Gaming
      - com.valvesoftware.Steam
      - net.lutris.Lutris
      # Utilities
      - com.github.tchx84.Flatseal
      - io.github.flattool.Warehouse
      - io.missioncenter.MissionCenter
    remove:
      # Aurora default apps to remove
      - io.github.kolunmi.Bazaar  # If you prefer a different app store
  user:
    repo-url: https://dl.flathub.org/repo/flathub.flatpakrepo
    repo-name: flathub
```

### Aurora's Default Flatpaks (for reference)

Aurora installs these by default via the `bazaar.preinstall` file:

| App | Flatpak ID | Keep/Remove? |
|-----|------------|--------------|
| Bazaar (App Store) | `io.github.kolunmi.Bazaar` | ☐ |

**Note**: Aurora's Flatpak list is minimal - most apps are user-installed via Bazaar.

---

### Flatpak Customization Checklist

| Task | Status |
|------|--------|
| Define system-scope Flatpaks to install | ☐ Todo |
| Define system-scope Flatpaks to remove | ☐ Todo |
| Define user-scope Flatpak remote (optional) | ☐ Todo |
| Test Flatpak installation on first boot | ☐ Todo |

---

## Part 6: Custom ujust Recipes

Add custom commands accessible via `ujust` for user convenience.

### Files to Create

| File | Purpose | Status |
|------|---------|--------|
| `files/usr/share/ublue-os/just/60-aegyx.just` | Custom ujust recipes | ☐ Todo |
| `scripts/register-justfiles.sh` | Register justfile imports | ☐ Todo |

### Example Custom Justfile

`files/usr/share/ublue-os/just/60-aegyx.just`:
```just
# Aegyx OS Custom Commands

# Show Aegyx system info
aegyx-info:
    @echo "╔══════════════════════════════════════╗"
    @echo "║         Aegyx OS Information         ║"
    @echo "╚══════════════════════════════════════╝"
    @fastfetch

# Toggle RGB lighting
aegyx-rgb ACTION="":
    #!/bin/bash
    if [ "{{ ACTION }}" = "off" ]; then
        openrgb --mode off
    elif [ "{{ ACTION }}" = "on" ]; then
        openrgb --mode direct
    else
        openrgb --gui
    fi

# Manage Btrfs snapshots
aegyx-snapshot ACTION="list":
    #!/bin/bash
    case "{{ ACTION }}" in
        create) sudo snapper create -d "Manual snapshot" ;;
        list) sudo snapper list ;;
        *) echo "Usage: ujust aegyx-snapshot [create|list]" ;;
    esac
```

### Registration Script

`scripts/register-justfiles.sh`:
```bash
#!/bin/bash
echo 'import "/usr/share/ublue-os/just/60-aegyx.just"' >> /usr/share/ublue-os/justfile
```

---

## Combined Recipe for Parts 4-6

```yaml
modules:
  # Part 4: Copy branding/theming files
  - type: files
    files:
      - source: usr
        destination: /usr

  # Part 4: Run branding scripts (symlinks, sed modifications)
  - type: script
    scripts:
      - branding.sh
      - register-justfiles.sh

  # Part 5: Flatpak applications
  - type: default-flatpaks
    notify: true
    system:
      repo-url: https://dl.flathub.org/repo/flathub.flatpakrepo
      repo-name: flathub
      install:
        - org.mozilla.firefox
        - org.libreoffice.LibreOffice
        - com.valvesoftware.Steam
        - com.github.tchx84.Flatseal
        - io.missioncenter.MissionCenter
      remove:
        # Remove apps you don't want
        - io.github.kolunmi.Bazaar
    user:
      repo-url: https://dl.flathub.org/repo/flathub.flatpakrepo
      repo-name: flathub
```

---

## Implementation Priority

| Priority | Item | Effort | Impact |
|----------|------|--------|--------|
| 1 | Part 1 Easy Additions | Low | High |
| 2 | Part 4 Branding & Theming | Medium | High (UX) |
| 3 | Part 5 Flatpak Applications | Low | High (UX) |
| 4 | Part 6 Custom ujust Recipes | Low | Medium |
| 5 | Part 2 Basic Gamescope | Low | Medium |
| 6 | Part 3 Valve's Patched Stack | High | High (gaming) |

**Recommendation**: Complete Parts 1, 4, 5, 6 first for a functional custom image. Parts 2/3 are gaming optimizations to add later.

---

## Progress Tracking

### Part 1 Checklist
- [ ] Add COPR repos to recipe
- [ ] Add Fedora packages to recipe
- [ ] Configure systemd services
- [ ] Test build
- [ ] Verify packages installed correctly
- [ ] Test greenboot rollback functionality
- [ ] Test snapper snapshot creation
- [ ] Test ds-inhibit with PlayStation controller
- [ ] Test openrgb with RGB hardware

### Part 2 Checklist
- [ ] Research gamescope without patched Mesa
- [ ] Test basic gamescope functionality
- [ ] Document limitations found

### Part 3 Checklist
- [ ] Study Bazzite implementation thoroughly
- [ ] Create test branch
- [ ] Document all repo configurations needed
- [ ] Implement in test environment
- [ ] Test graphics, audio, Bluetooth
- [ ] Create rollback procedure
- [ ] Merge to main if successful

### Part 4 Checklist (Branding & Theming)
- [ ] Create `files/` directory structure in repo
- [ ] Design/obtain custom logos (SVG + PNG)
- [ ] Create distributor-logo.svg
- [ ] Create distributor-logo-symbolic.svg
- [ ] Create banner logo (aegyx-banner.svg)
- [ ] Generate PNG variants via script
- [ ] Create/customize wallpapers
- [ ] Create KDE look-and-feel package (dev.aegyx.desktop)
- [ ] Configure SDDM theme
- [ ] Configure Plymouth boot splash
- [ ] Write branding.sh script for symlinks/sed modifications
- [ ] Modify taskbar default pinned apps
- [ ] Update os-release branding
- [ ] Test all branding elements display correctly

### Part 5 Checklist (Flatpak Applications)
- [ ] Define list of system Flatpaks to install
- [ ] Define list of Aurora default Flatpaks to remove (if any)
- [ ] Add default-flatpaks module to recipe
- [ ] Test first-boot Flatpak installation
- [ ] Verify removed apps are not present
- [ ] Verify installed apps work correctly

### Part 6 Checklist (Custom ujust Recipes)
- [ ] Create `files/usr/share/ublue-os/just/60-aegyx.just`
- [ ] Write custom ujust commands
- [ ] Create `scripts/register-justfiles.sh`
- [ ] Test `ujust` commands after build

---

*Last updated: 2026-01-05*
