# Estructura del repositorio

```text
dotfiles/
├── .config/
│   ├── alacritty/  cava/  dolphin/  fastfetch/  foot/  ghostty/  kitty/  nvim/  wezterm/  yazi/
│   ├── hypr/             hyprland.lua + modules/ (appearance, hardware, input, rules, startup, system)
│   ├── matugen/          config.toml, templates/, scripts/reload-waybar.sh
│   ├── niri/  mango/     otros compositores
│   ├── quickshell/OozeShell/    lo extrae el instalador
│   ├── waybar/           configuraciones, waybar-launcher.sh, scripts/, themes/
│   ├── rofi/  swaync/  swayosd/
│   ├── VK-Th/themes/     tema de Vesktop
│   └── starship.toml
├── DotDocs/              documentación detallada
├── install-oozedots.sh   instalador
├── update-oozedots.sh    actualizador
├── INSTALLER.md          instalador, bloque por bloque
├── UPDATING.md           guía del actualizador
├── NixOSInstallation.md  dependencias para NixOS
├── OozeShell.zip         compilación única para todos los sistemas
├── OozeWalls.zip         motor opcional de fondos en vivo (Rust)
├── git-update  nix-rofi  random-fastfetch  .zshrc
├── SuwayomiSetup.md      se genera al omitir Suwayomi en Arch
└── screenshots/  README.md
```

## Interior de OozeShell

Cada módulo tiene su nota en `OozeShell/Docs/Architecture/`.

```text
OozeShell/
├── shell.qml                 punto de entrada, popups, manejadores de IPC
├── Modules/                  Bar, Pill, Dock, Dashboard, Menu, Overview, Lock, Power,
│                             Taskbar, Tray, Widgets, Keybinds, SystemMonitor, Privacy
├── Services/                 Audio, OozeAudio, Bluetooth, Network, Battery, Brightness,
│                             Clipboard, Notify, Agenda, Mpris, Launcher, PackageSearch,
│                             Walls, Screenshot, Monitor, DefaultApps, Caps, Lang, Caffeine,
│                             Agents
├── Settings/                 páginas de ajustes y registro
├── Core/                     configuración, estado y objetivos de IPC
├── Backends/                 native/ y python/ (ooze-daemon.py)
├── COMMON/                   widgets y estado compartidos
├── Assets/                   fuentes y logos
├── Docs/                     notas de arquitectura
├── native/                   núcleo C++ opcional
└── Tools/                    check.sh, build-native.sh, ooze-run.sh, scripts de captura y fondos, fuentes
```
