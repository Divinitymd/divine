# ✦ Divine Linux

> A beautifully crafted Linux distribution for people who love great design.
> Built on Ubuntu 24.04 LTS · Hyprland Wayland compositor · Caelestia shell

---

## Overview

Divine Linux is a custom Ubuntu-based distribution featuring:

- **Hyprland** — a dynamic tiling Wayland compositor with buttery-smooth animations
- **Caelestia** (end-4/dots-hyprland) — a stunning, design-forward shell with Material You theming
- **Calamares** — a working graphical installer (partition, users, bootloader — all configured)
- **SDDM** — custom Divine login screen
- **Dark-first design** — Papirus-Dark icons, adw-gtk3-dark GTK theme, JetBrains Mono font

---

## Project Structure

```
divine-linux/
├── build.sh                          # Main build script
├── auto/
│   ├── config                        # live-build auto configuration
│   └── clean                         # cleanup script
├── config/
│   ├── package-lists/
│   │   └── divine.list.chroot        # All packages to install
│   ├── hooks/live/
│   │   ├── 0100-system.hook.chroot   # Base system, PPAs, OS identity
│   │   ├── 0200-hyprland.hook.chroot # Hyprland + SDDM setup
│   │   ├── 0300-caelestia.hook.chroot# AGS, swww, Caelestia dotfiles
│   │   ├── 0400-calamares.hook.chroot# Calamares + live user + launcher
│   │   ├── 0500-branding.hook.chroot # Wallpaper, logo, GRUB, Plymouth
│   │   └── 0600-finalize.hook.chroot # Env, sysctl, caches, cleanup
│   └── includes.chroot/
│       ├── etc/
│       │   ├── calamares/
│       │   │   ├── settings.conf     # Calamares main config
│       │   │   ├── branding/divine/  # Branding: name, colors, slideshow
│       │   │   └── modules/          # All installer module configs
│       │   ├── skel/                 # Default dotfiles for all users
│       │   │   └── .config/
│       │   │       ├── hypr/         # Hyprland, hyprlock, hypridle, hyprpaper
│       │   │       ├── waybar/       # Status bar config + CSS
│       │   │       ├── wofi/         # App launcher style
│       │   │       ├── kitty/        # Terminal config + color scheme
│       │   │       ├── dunst/        # Notification daemon
│       │   │       ├── gtk-3.0/      # GTK3 theme settings
│       │   │       └── gtk-4.0/      # GTK4 theme settings
│       │   └── systemd/system/
│       │       └── divine-first-boot.service
│       └── usr/
│           ├── bin/
│           │   ├── divine-installer  # Calamares launcher (with pkexec)
│           │   └── divine-welcome    # GTK4/Adwaita welcome app
│           └── lib/divine/
│               └── first-boot.sh     # Runs once after installation
```

---

## Build Requirements

A Debian/Ubuntu host system (22.04+ or 24.04) with:

```bash
sudo apt-get install \
  live-build debootstrap git curl wget \
  squashfs-tools xorriso isolinux \
  syslinux-utils grub-efi-amd64-bin \
  grub-pc-bin mtools
```

---

## Building the ISO

### Full build (recommended)
```bash
git clone https://github.com/your-org/divine-linux.git
cd divine-linux
chmod +x build.sh auto/config auto/clean
sudo ./build.sh build
```

The ISO will be at `output/divine-linux-YYYYMMDD.iso`.

### Step-by-step
```bash
# 1. Configure live-build
sudo ./build.sh configure

# 2. Build (no clean)
sudo ./build.sh rebuild

# 3. Clean everything and start over
sudo ./build.sh clean
```

### Build time
- First build: ~45–90 minutes (downloads ~2–3 GB)
- Rebuild (no clean): ~15–30 minutes

---

## Calamares Installer

The installer is fully configured for:

| Module | Description |
|--------|-------------|
| Welcome | System requirements check |
| Locale | Timezone + language (GeoIP aware) |
| Keyboard | Layout selection |
| Partition | Auto / manual, ext4 / btrfs / xfs, LUKS encryption |
| Users | Username, password, sudo, autologin |
| Summary | Review before install |
| Install | squashfs unpack → real system |
| Bootloader | GRUB for both BIOS and UEFI |
| Finished | Reboot prompt |

### Launching the installer (live session)
- **Super + Shift + I** — keyboard shortcut
- **Desktop icon** — "Install Divine Linux"
- **Welcome app** — auto-starts on login

---

## Customization

### Change the wallpaper
Replace `/usr/share/divine/wallpaper.jpg` in your build, then update:
- `config/includes.chroot/etc/skel/.config/hypr/hyprpaper.conf`
- `config/includes.chroot/etc/skel/.config/hypr/divine.conf` (swww line)

### Add packages
Edit `config/package-lists/divine.list.chroot`.

### Modify Hyprland keybinds
Edit `config/includes.chroot/etc/skel/.config/hypr/divine.conf`.

### Change the Caelestia source
In `config/hooks/live/0300-caelestia.hook.chroot`, update the `git clone` URL.

### Calamares modules
All configs live in `config/includes.chroot/etc/calamares/modules/`. To add a module, add it to `settings.conf` sequence and create its `.conf` file.

---

## Post-Installation

On first boot after Calamares installs Divine:

1. `divine-first-boot.service` runs automatically
2. Pulls fresh Caelestia dots for the new user
3. Installs oh-my-zsh
4. Adds Flathub remote
5. Removes live-only packages (calamares, live-boot, etc.)
6. Disables itself permanently

---

## Default Keybindings

| Keys | Action |
|------|--------|
| Super + Return | Kitty terminal |
| Super + Space | Wofi app launcher |
| Super + E | Thunar file manager |
| Super + B | Firefox |
| Super + Q | Close window |
| Super + F | Fullscreen |
| Super + V | Toggle floating |
| Super + Backspace | Lock screen (hyprlock) |
| Super + Shift + I | Launch installer |
| Super + 1–5 | Switch workspace |
| Super + Shift + 1–5 | Move to workspace |
| Print | Screenshot (region → clipboard) |
| Super + Shift + S | Screenshot (region → file) |
| XF86Audio* | Volume control |
| XF86Brightness* | Brightness control |

---

## Credits

- [Hyprland](https://hyprland.org) — Compositor
- [end-4/dots-hyprland](https://github.com/end-4/dots-hyprland) — Caelestia dotfiles
- [Calamares](https://calamares.io) — Installer framework
- [Ubuntu](https://ubuntu.com) — Base system
- [Papirus](https://github.com/PapirusDevelopmentTeam/papirus-icon-theme) — Icons
- [adw-gtk3](https://github.com/lassekongo83/adw-gtk3) — GTK theme

---

*Divine Linux — made for those who refuse to compromise on beauty.*
