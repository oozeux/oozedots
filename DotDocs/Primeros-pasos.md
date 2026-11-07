# Primeros pasos

Guía para quien llega nuevo a Linux o a este repositorio.

## Glosario

| Palabra | Significado |
| --- | --- |
| **Terminal** | Ventana donde se escriben comandos. |
| **Dotfiles** | Archivos de configuración de tus programas (como `.zshrc`). |
| **Hyprland** | Compositor de Wayland: dibuja y organiza las ventanas. Reemplaza a GNOME o KDE. |
| **Wayland** | La forma moderna en que Linux dibuja los gráficos. |
| **Quickshell / OozeShell** | Quickshell construye shells de escritorio; OozeShell es el de este repositorio. |
| **Paquete** | Programa instalable mediante un gestor (`pacman` en Arch, `nix` en NixOS). |
| **AUR** | Repositorio comunitario de Arch; requiere un ayudante como `paru` o `yay`. |
| **Symlink** | Archivo-atajo: `~/.config/hypr` apunta a la carpeta del repositorio. |
| **`~`** | Tu carpeta personal, por ejemplo `/home/tunombre`. |
| **`sudo`** | Ejecuta un comando como administrador. |
| **TTY** | Pantalla solo de texto (`Ctrl+Alt+F3`; volver con `Ctrl+Alt+F1` o `F2`). |

## Sistemas compatibles

| Sistema | Soporte |
| --- | --- |
| **NixOS** | Objetivo principal. Búsqueda de paquetes con `nix-search`. |
| **Arch Linux** | Compatible. El instalador instala las dependencias; búsqueda con pacman + AUR. No se instala nada de Nix. |
| **Otras distros** | No compatibles. Se genera `NixOSInstallation.md` con la lista de dependencias para adaptarla a mano. |

Para saber qué sistema usas: `cat /etc/os-release` y mira la línea `NAME=`.

## Antes de instalar

* Arch o NixOS funcionando, con internet y un usuario normal con `sudo`.
* Entre 15 y 30 minutos.
* Poder cerrar sesión al terminar.

Si aún no tienes Arch, sigue la [guía oficial](https://wiki.archlinux.org/title/Installation_guide) (`archinstall` es lo más simple).

## Qué revisar de tu equipo

* Monitores: `hypr/modules/hardware`.
* Variables de NVIDIA, teclado, apps de inicio, atajos y terminal.

Las rutas usan `$HOME`; no hace falta escribir tu usuario.

## Después de instalar

1. Cierra sesión y elige **Hyprland** (o ejecuta `Hyprland` desde un TTY, nunca con `sudo`).
2. `SUPER+I` muestra los atajos. `SUPER` es la tecla con el logo de Windows.
3. `SUPER+L` elige monitor y `SUPER+G` el idioma.
4. Pon imágenes en `~/Pictures/Wallpapers` y abre el selector de fondos.
5. Abre **Ajustes** para cambiar barra, estilo, colores y fuentes.

Para iniciar OozeShell a mano: `quickshell -c OozeShell`.
