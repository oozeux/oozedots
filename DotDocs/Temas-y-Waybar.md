# Temas, fondos y Waybar

## Matugen

Genera los colores a partir del fondo actual y da tema a Hyprland, OozeShell, Waybar, terminales, Cava, Starship, Yazi, Vesktop y más.

```text
~/.config/matugen/config.toml      configuración principal
~/.config/matugen/templates/       una plantilla por app
```

## Fondos

* Se leen de `~/Pictures/Wallpapers` (créala si no existe).
* Estáticos: `awww`. Video: `mpvpaper` u OozeWalls; Matugen toma la paleta de un fotograma.
* Fondo de la pantalla de bloqueo: **Configuración avanzada → General**.
* Capturas en `Imágenes/Screenshots` y grabaciones en `Videos/Recordings`; ambas configurables en **Configuración avanzada → Capturas y grabación**.

## Waybar

OozeShell puede usar Waybar en vez de su barra: **Ajustes → Barra → Waybar** (solo horizontal, no Pill). Desactivado hasta instalar `waybar`.

* `waybar/waybar-launcher.sh`: lo inicia OozeShell; mantiene una sola Waybar, elige la configuración del compositor activo (`config.jsonc`, `config-niri.jsonc`, `config-mango.jsonc`) y la cierra al volver a la barra de OozeShell.
* En cada cambio de fondo, Matugen reescribe `waybar/style.css` y `matugen/scripts/reload-waybar.sh` envía `SIGUSR2` para recargar colores. Nunca inicia ni mata Waybar.
* Registro: `$XDG_RUNTIME_DIR/waybar-ctl/reload.log`.

**Temas estáticos**: `waybar/themes/`, con `waybar/themes/waybar-theme-switcher.sh`. Matugen los sobrescribe en el siguiente cambio de fondo; para conservarlos, desactiva `[templates.waybar]` en `~/.config/matugen/config.toml`.
