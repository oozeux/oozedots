# Componentes opcionales

OozeShell funciona sin ninguno.

## Núcleo nativo de animación (C++)

Hace las matemáticas de animación de forma nativa; sin él se usa QML puro.

```bash
bash ~/.config/quickshell/OozeShell/Tools/build-native.sh
```

* El instalador lo ofrece y añade `QML_IMPORT_PATH` a `env.lua`; cierra sesión una vez.
* Activo cuando el log dice `[theme] núcleo nativo: activo`.
* Requiere `cmake` y un compilador C++17.
* NixOS: `nix-shell native/shell.nix --run 'bash Tools/build-native.sh'`; el Qt debe coincidir con el de `quickshell`.

## OozeWalls (fondos en vivo, Rust)

* Viaja como `OozeWalls.zip` en la raíz; se extrae en `~/.local/share/oozewalls/src`.
* Instala `oozewalls`, `oozewalls-gui` y `oozewalls-live` en `~/.local/bin`, más una entrada en el lanzador.
* En Arch instala Rust y las librerías necesarias; el selector de carpetas requiere `xdg-desktop-portal` y un backend FileChooser.
* NixOS: módulo de Home Manager (`hm-module.nix`, dentro del zip).
* Se elige en **Ajustes → Wallpapers**. OozeShell restaura el fondo al iniciar; no hace falta `exec-once`.

## Suwayomi (Arch)

Servidor de manga. El instalador puede instalar `suwayomi-server-bin`, crear `tachidesk.service` y el comando `tachidesk` (`start|stop|restart|status|enable|disable`). Se controla desde **Ajustes → Servicios** y no arranca con el sistema. Si lo omites, los pasos quedan en `SuwayomiSetup.md`.

## Comando `git-update`

No se enlaza automáticamente. Hace `git add`, `git commit` y `push` en un paso.

```bash
chmod +x ~/dotfiles/git-update
ln -s ~/dotfiles/git-update ~/.local/bin/git-update
git-update -Tu comentario aquí
```

El guion `-` va pegado al comentario.
