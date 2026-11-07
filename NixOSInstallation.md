# Dependencias de OozeDots / OozeShell para NixOS

Esta lista es la misma que usa la rama de Arch del instalador. Está escrita como
«lo que necesitas tener disponible» y no como nombres exactos de atributos de
Nix, porque esos nombres cambian entre canales y overlays.

## Atajo: sáltate todo esto

Puedes usar directamente estos dos repositorios, que ya traen todo resuelto:

- Configuración del sistema lista para usar: https://github.com/shizukutakahashi55-del/nix-home
- Dotfiles de usuario listos para OozeShell: https://github.com/shizukutakahashi55-del/oozedots

## Cómo está ordenada esta lista

1. [Base](#1-base): lo mínimo para que el escritorio arranque.
2. [Terminal y fuentes](#2-terminal-y-fuentes)
3. [Opcionales](#3-opcionales): núcleo nativo, OozeWalls, NixSearch, Waybar y otros compositores.
4. [Herramientas que OozeShell reemplaza](#4-herramientas-que-oozeshell-reemplaza)
5. [Extras](#5-extras-desarrollo-juegos-y-utilidades)

---

## 1. Base

### Compositor

- Hyprland: `hyprland`, `hyprpolkitagent`, `hyprpaper`, `hyprshot`,
  `hyprsunset`, `hyprshutdown`, `hyprsysteminfo`, `hypridle`, `hyprlock`
- `awww` (el sucesor actual de `swww`)

### Shell

- Quickshell (`quickshell-git`): incluye el soporte de Qt Quick
- Qt6: QtQuick, QtQuick.Controls, QtQuick.Layouts, QtQuick.Shapes,
  QtQuick.Effects, QtQml y `qt6ct`
- `matugen` (genera la paleta a partir del fondo de pantalla)
- `python3` 3.11 o superior (`palette-apply.py` aplica las paletas fijas a las
  plantillas de matugen: terminales, Hyprland, Qt/KDE, etc.)

### Audio

- `wpctl`, `pw-dump`, `pw-link`, `pw-cli`, `pw-loopback` (PipeWire + WirePlumber)
- `pactl` (`libpulse`; la grabación de pantalla lo usa para elegir el dispositivo de audio)
- `pavucontrol`, `qpwgraph` (grafo de rutas de audio), `cava`
- `playerctl`

### Hardware, red y Bluetooth

- `brightnessctl`
- `powerprofilesctl` (`power-profiles-daemon`)
- `upower`
- `nmcli` (NetworkManager) y `networkmanagerapplet`
- `bluetoothd` (BlueZ) y Blueman

### Medios y capturas

- `mpvpaper` (+ `mpv`), `ffmpeg`, `socat`
- `grim`, `slurp`
- `wf-recorder` (grabación de pantalla)

### Utilidades

- `wl-copy` y `wl-paste` (`wl-clipboard`)
- `cliphist` (historial del portapapeles; también necesita `wl-clipboard`)
- `xdg-utils` (`xdg-open`)
- `xdg-user-dirs` (`xdg-user-dir`; localiza tus carpetas Imágenes y Vídeos)
- `jq`, `curl` (fondos en línea y descargas), `pgrep`
- `tmux` (sesiones del Launcher)
- `zsh` (los dotfiles enlazan un `.zshrc`)
- `rofi` (Launcher y PowerMenu alternativos)
- `fastfetch`
- `libnotify` (`notify-send`), `imagemagick`, `wtype`, `wev`

## 2. Terminal y fuentes

- Una terminal: `foot`, `kitty`, `alacritty`, `wezterm` o `ghostty`
- Nerd Fonts: «JetBrainsMono Nerd Font» y «Symbols Nerd Font Mono»
- Una fuente de emoji a color (Noto Color Emoji)

---

## 3. Opcionales

Nada de esto es obligatorio: OozeShell funciona sin ello.

### Pequeños extras

- GPU: `nvidia-smi` (NVIDIA) o los contadores sysfs de amdgpu, para las estadísticas
- Alarma de la lista de tareas: `pw-play` o `paplay` (PipeWire / libpulse)
- Descargas de fondos en línea más rápidas: `aria2` (si falta, se usa `curl`)
- Guardado más seguro de las claves de los agentes de IA: `libsecret`
  (`secret-tool`) y un llavero como `gnome-keyring` (si falta, se usa un
  archivo cifrado con `openssl`)

### Núcleo nativo de animación (C++)

OozeShell incluye un pequeño módulo en C++ (`OozeShell.Native`). Sin él todo
sigue funcionando en QML puro; con él, los cálculos de animación se hacen de forma nativa.

- Para compilarlo: `cmake` 3.21 o superior, un compilador C++17 y Qt6
  (`qtbase` + `qtdeclarative`, con los headers de desarrollo). En NixOS usa el
  shell incluido:

```bash
nix-shell ~/.config/quickshell/OozeShell/native/shell.nix \
  --run 'bash ~/.config/quickshell/OozeShell/Tools/build-native.sh'
```

- El Qt con el que compilas **debe ser el mismo** que usa tu `quickshell`; si no,
  Qt rechaza el plugin (detalles en `native/shell.nix`).
- Quickshell necesita saber dónde está el módulo. Se indica una sola vez en el
  entorno de la sesión de Hyprland (el instalador lo añade a `env.lua` cuando la
  compilación funciona):

```lua
hl.env("QML_IMPORT_PATH", os.getenv("HOME") .. "/.local/share/oozeshell/qml")
```

- Al arrancar, OozeShell escribe en el log `[theme] núcleo nativo: activo` (o el
  motivo por el que no está disponible).

### Motor OozeWalls

OozeWalls viaja como `OozeWalls.zip` en la raíz de los dotfiles. El instalador lo
extrae en `~/.local/share/oozewalls/src`, lo compila ahí y copia `oozewalls`,
`oozewalls-gui` y `oozewalls-live` a `~/.local/bin`.

- Compilación: `cargo` + `rustc` + `gcc` + `pkg-config`
- Ejecución: wayland, libxkbcommon, mesa/libglvnd (GL/EGL), fontconfig,
  freetype, libX11, libXcursor, libXrandr, libXi
- `xdg-desktop-portal` (+ un backend de FileChooser como
  `xdg-desktop-portal-gtk`) para el selector de carpetas de la app
- `mpvpaper` + `mpv` + `socat` para los fondos en vivo

### NixSearch

- `nix-search` / `nix-search-cli` (la búsqueda se ejecuta desde el panel NixSearch de OozeShell)
- `nix` (`nix shell` / `nix profile install` desde NixSearch)

### Barra alternativa

- `waybar`: backend de barra que se elige en OozeShell (Ajustes → Barra). Su
  configuración siempre se enlaza; solo el paquete es opcional.

### Otros compositores

- `niri` o `mango` (`mmsg`): OozeShell los detecta por su cuenta.

---

## 4. Herramientas que OozeShell reemplaza

Ya no se necesitan para el uso normal (las notificaciones, el launcher y el
cierre de sesión están integrados en OozeShell), pero sus configuraciones se
quedan en los dotfiles por si quieres usarlas por separado:

- `swaync`
- `swayosd`
- `wlogout`

`rofi` **no** está en esta lista: es un backend de primera clase (Ajustes
avanzados → Launcher / PowerMenu) y su configuración siempre se instala.

## 5. Extras: desarrollo, juegos y utilidades

`greetd`, `udisks2`, `pw-viz`, `rtkit`, `flatpak`, `zip`, `unzip`, `yazi`,
`neovim`, `starship`, `fd`, `ripgrep`, `dolphin`, `nomacs`, tema oscuro estilo
Adwaita, cursores Bibata-Modern-Classic, `gamemode`, `lutris`, `mangohud`,
`prismlauncher`, `protonplus`, `wine`, `protontricks`, `goverlay`, `curl`, `git`,
`gh`, `jdk21`, `lazygit`, `python3`, `python3Packages.pip`, `vscodium-fhs`,
`nixd`, `nixfmt-rfc-style`, `ruff`, `tree`, `qt6.qtquick3d`,
`qt6.qtdeclarative`, `uv`, `wget`, `vim`
