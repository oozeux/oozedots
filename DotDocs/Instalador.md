# Instalador

Para el resumen de instalación ver el [README](../README.md). Explicación bloque por bloque en [INSTALLER.md](../INSTALLER.md).

## Qué hace

En cualquier sistema:

1. Extrae `OozeShell.zip` en `.config/quickshell/OozeShell` (la copia anterior va a `~/.local/share/oozeshell/backups/`).
2. Crea los enlaces simbólicos y da permisos de ejecución a los scripts.

Solo en Arch:

1. Configura un ayudante de AUR (ofrece `paru`) y opcionalmente actualiza el sistema.
2. Instala dependencias base, `quickshell` y `awww`.
3. Instala la terminal elegida y, si lo pides, servicios, Waybar, herramientas heredadas, extras y Suwayomi.
4. Opcionalmente compila el núcleo nativo y OozeWalls.
5. Activa las variables de NVIDIA y Electron en `env.lua`.

En otras distros solo escribe `NixOSInstallation.md` y enlaza `nix-rofi`.

## Opciones

| Opción | Efecto |
| --- | --- |
| `-y`, `--yes` | Acepta la respuesta sugerida a todo. |
| `-h`, `--help` | Muestra la ayuda. |

## Preguntas y qué responder

Responde `y` o `n`; `Enter` acepta la sugerida.

| Pregunta | Si dudas | Notas |
| --- | --- | --- |
| `Are you installing on Arch Linux?` | `y` en Arch | `n` no instala paquetes. |
| `Install the AUR helper 'paru'?` | `y` | `matugen`, `mpvpaper`, `awww` y algunas fuentes pueden requerirlo. |
| `Update the whole system first?` | `y` | Evita problemas de versiones. |
| `Which terminal should I install?` | Cualquiera | `foot` es ligera, `kitty` amigable. |
| `Enable NetworkManager, Bluetooth and power-profiles-daemon?` | `y` | `n` solo si usas `iwd` u otra herramienta. |
| `Install Waybar?` | `n` | Barra opcional. |
| `Also install swaync, swayosd and wlogout?` | `n` | OozeShell ya los reemplaza. |
| `Install the extras bundle?` | `n` | Descarga grande. |
| `Install the Suwayomi manga server?` | `n` | Pasos en `SuwayomiSetup.md`. |
| `Start Tachidesk right after installing it?` | `n` | Nunca arranca con el sistema. |
| `Enable the Nvidia variables in Hyprland's env.lua?` | Según GPU | Comprueba con `lspci \| grep -i nvidia`. |
| `Build the native animation core?` | `y` | Opcional, cerca de un minuto. |
| `Build the OozeWalls wallpaper engine?` | `n` | Solo aparece si existe `OozeWalls.zip`. |
| `What if a config already exists in ~/.config?` | «Ask me each time» | Otras: reemplazar todo o conservar los tuyos. |
| `Replace it with the dotfiles version?` | `y` | Tu archivo pasa a `.bak-<fecha>`. |
| `Start the installation?` | `y` | Antes de esto no se ha cambiado nada. |

## Qué se enlaza

Todo apunta a `~/dotfiles/.config`.

| Siempre | Con «herramientas heredadas» | Fuera de Arch |
| --- | --- | --- |
| `alacritty` `cava` `dolphin` `fastfetch` `foot` `ghostty` `hypr` `kitty` `matugen` `nvim` `quickshell` `rofi` `wezterm` `yazi` `waybar` `starship.toml` `.zshrc` | `swaync` `swayosd` | `~/.local/bin/nix-rofi` |

También `niri`, `mango` (si existen) y `VK-Th/themes` → `~/.config/vesktop/themes`.

| Situación en el destino | Resultado |
| --- | --- |
| Nada | Se crea el enlace. |
| Enlace correcto | Se deja igual. |
| Enlace a otro lugar | Se reemplaza. |
| Archivo o carpeta real | Pregunta si moverlo a `<destino>.bak-<fecha>`. |
