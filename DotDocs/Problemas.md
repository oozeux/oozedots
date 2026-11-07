# Problemas frecuentes

Primer paso casi siempre: ejecutar el programa desde una terminal y leer el error.

## Instalador

| Problema | Solución |
| --- | --- |
| `Could not find .../OozeShell.zip` | Pon `OozeShell.zip` en `~/dotfiles` y reejecuta. |
| No aparece la pregunta de OozeWalls | Falta `OozeWalls.zip` en `~/dotfiles`. |
| `Dotfiles directory not found` | El repositorio debe estar en `~/dotfiles`. |
| Se niega a ejecutarse | Úsalo como usuario normal, sin `sudo`. |
| `Permission denied` | `chmod +x install-oozedots.sh` |
| Pregunta por un archivo existente | `y` respalda y enlaza; `n` conserva el tuyo. |
| Paquetes de Arch sin instalar | Se listan al final. Busca con `pacman -Ss` o `paru -Ss`; `matugen`, `mpvpaper`, `awww` y fuentes pueden requerir AUR. |
| Aviso de `hyprland` anterior a 0.55 | Actualiza Hyprland. |
| OozeWalls no compila | `bash ~/.local/share/oozewalls/src/install.sh` y verifica `cargo --version`. |
| No se encuentra `nix-rofi` o `git-update` | Revisa que `~/.local/bin` esté en `$PATH`. `git-update` nunca se enlaza solo. |

## OozeShell

| Problema | Solución |
| --- | --- |
| No aparece nada tras iniciar sesión | Ejecuta `quickshell -c OozeShell` en una terminal y lee los errores. |
| No arranca | `which quickshell`; luego `bash Tools/check.sh` desde la carpeta de OozeShell. |
| Barra o fondo no se ven | El monitor por defecto es `DP-3`. `SUPER+L` o `oozeshell monitor set auto`; lista con `hyprctl monitors`. |
| Iconos como cuadrados vacíos | Instala JetBrainsMono Nerd Font y Symbols Nerd Font Mono. |
| Emoji como cajas | Instala `noto-fonts-emoji`. |
| La búsqueda de paquetes no hace nada | NixOS: `which nix-search`. Arch: verifica `pacman` y `paru`/`yay`. |
| No se listan fondos | `mkdir -p ~/Pictures/Wallpapers` o cambia la carpeta en **Ajustes → Wallpapers**. |
| La grabación no funciona | Verifica `wf-recorder`; errores en `~/.cache/oozeshell/rec.log`. |
| Capturas o grabaciones en la carpeta personal | `xdg-user-dirs-update` o define las carpetas en **Configuración avanzada → Capturas y grabación**. |
| Hyprland ignora cambios de apariencia | `hyprctl reload`. |
| Falla la compilación nativa (`CMakeCache.txt ... different`) | `rm -rf ~/dotfiles/.config/quickshell/OozeShell/native/AnimationController/build` y recompila. |
| Qt rechaza el núcleo nativo (NixOS) | El Qt de `native/shell.nix` debe coincidir con el de `quickshell`. |
| El tema de Vesktop no aparece | El enlace debe apuntar a `~/.config/vesktop/themes`; reejecuta el instalador. |

## Waybar y Matugen

| Problema | Solución |
| --- | --- |
| Opción Waybar desactivada | Instala `waybar`. |
| Los colores no cambian | Lee `$XDG_RUNTIME_DIR/waybar-ctl/reload.log`: «SIGUSR2 sent» = señal enviada; «no pidfile» = OozeShell no inició esa Waybar. Verifica que `matugen/scripts/reload-waybar.sh` sea ejecutable. |
| Queda una Waybar al volver a la barra de OozeShell | `pkill -x waybar` y vuelve a cambiar. Solo debe iniciarse con `waybar-launcher.sh`. |
| Un tema estático se sobrescribe | Desactiva `[templates.waybar]` en `~/.config/matugen/config.toml`. |
| Icono de notificaciones vacío | `waybar/scripts/notify-state.sh` necesita OozeShell en ejecución. |
