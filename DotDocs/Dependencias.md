# Dependencias

En Arch las instala el instalador. En NixOS usa [nix-home](https://github.com/shizukutakahashi55-del/nix-home) o `NixOSInstallation.md`.

| Grupo | Paquetes |
| --- | --- |
| Compositor | `hyprland` (≥ 0.55), `hyprpaper`, `hypridle`, `hyprlock`, `hyprpolkitagent`, `hyprshot`, `hyprsunset`, `hyprshutdown`, `hyprsysteminfo`, `xdg-desktop-portal` (+ `-hyprland`, `-gtk`) |
| Shell | `quickshell`, `qt6-base`, `qt6-declarative`, `qt6-svg`, `qt6-shadertools`, `qt6ct` |
| Audio | `pipewire`, `pipewire-pulse`, `wireplumber`, `libpulse`, `pavucontrol`, `qpwgraph` |
| Hardware | `brightnessctl`, `power-profiles-daemon`, `upower` |
| Red y Bluetooth | `networkmanager` + applet, `bluez`, `bluez-utils`, `blueman` |
| Medios | `playerctl`, `mpv`, `mpvpaper`, `ffmpeg`, `cava` |
| Capturas y grabación | `grim`, `slurp`, `wf-recorder` |
| Fondos y temas | `awww`, `imagemagick`, `matugen` |
| Utilidades | `socat`, `jq`, `wl-clipboard`, `cliphist`, `xdg-utils`, `xdg-user-dirs`, `libnotify`, `wtype`, `wev`, `fastfetch`, `tmux`, `curl`, `python` (≥ 3.11), `zsh`, `rofi` |
| Fuentes | JetBrainsMono Nerd Font, Symbols Nerd Font Mono, Noto Color Emoji |
| Terminal | foot, kitty, alacritty, wezterm o ghostty |

## Para compilar partes opcionales (Arch)

| Parte | Paquetes |
| --- | --- |
| Núcleo nativo (C++) | `cmake`, `pkgconf` |
| OozeWalls (Rust) | `rust`, `pkgconf`, `mpv`, `wayland`, `libxkbcommon`, `mesa`, `libglvnd`, `fontconfig`, `freetype2`, `libx11`, `libxcursor`, `libxrandr`, `libxi`, `xdg-desktop-portal`, `xdg-desktop-portal-gtk` |

## Opcionales

* `waybar`, `swaync`, `swayosd`, `wlogout`.
* `nix-search` (búsqueda de paquetes en NixOS).
* `aria2`: descargas de fondos más rápidas (si no, `curl`).
* `libsecret` + llavero (`gnome-keyring`): claves de API de agentes de IA; sin ellos se usa un archivo cifrado.
* `nvidia-smi`: estadísticas de GPU.
* Fuentes pixel del tema CoOzey: Pixelify Sans, Jersey 10, Silkscreen, VT323, DotGothic16. Ejemplo para NixOS en `OozeShell/Tools/fonts/fonts.nix.example`. En Arch solo se instalan Silkscreen, VT323 y DotGothic16.
* Paquete de extras en Arch (yazi, neovim, starship, herramientas de juegos).
* Niri o MangoWM: OozeShell los detecta. Niri funciona bien; MangoWM sigue en desarrollo y está poco probado (v0.17.4 en NixOS).
