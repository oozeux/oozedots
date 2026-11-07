# Actualizar OozeDots

OozeDots incluye un actualizador que pone al día tu instalación con un solo comando.

## Requisitos

- OozeDots se instaló con `install-oozedots.sh`.
- Los dotfiles están en `~/dotfiles`.
- Lo ejecutas con tu usuario normal, no como root.

## Actualizar

```bash
bash ~/dotfiles/update-oozedots.sh
```

El actualizador muestra qué hay de nuevo, lo aplica y pregunta antes de hacer algo opcional. Al terminar, ofrece recargar Hyprland y reiniciar OozeShell.

Para saber si hay una actualización disponible sin cambiar nada:

```bash
bash ~/dotfiles/update-oozedots.sh --check
```

## Opciones

| Opción | Descripción |
|---|---|
| `--check` | Informa si hay una actualización disponible; no cambia nada |
| `--system` | También ejecuta una actualización completa del sistema (solo Arch) |
| `--force` | Repite la extracción, la compilación nativa y la revisión de enlaces aunque no haya cambios |
| `--replace` | Respalda y reemplaza los archivos reales que bloquean un enlace, sin preguntar |
| `--keep` | Conserva los archivos reales que bloquean un enlace, sin preguntar |
| `-y`, `--yes` | No pregunta; usa la respuesta segura en cada pregunta |
| `-h`, `--help` | Muestra la ayuda |

## Qué hace, en orden

1. **Comprobación.** Revisa que existe una instalación, detecta tu distro y consulta el remoto. Si no hay nada nuevo, termina con «Already up to date».
2. **Repositorio.** Aparta tus cambios locales (stash), actualiza los dotfiles y los vuelve a aplicar.
3. **Arch** (solo en Arch):
   - Actualización del sistema, si la aceptas.
   - Paquetes base nuevos desde tu última ejecución.
   - Variantes que falten (`quickshell`, `awww`, `wl-clipboard`, `cliphist`).
   - Refresco del comando `tachidesk`, si lo tienes.
4. **OozeShell.** Extrae `OozeShell.zip` si cambió y recompila el núcleo nativo si cambió OozeShell o Qt.
5. **Enlaces y permisos.** Revisa los enlaces simbólicos, da permisos de ejecución, migra las llamadas IPC antiguas de tus atajos y actualiza el respaldo de fuentes (fontconfig).
6. **Entorno.** Reaplica los cambios de `env.lua` y, si `OozeWalls.zip` cambió y ya tenías OozeWalls instalado, vuelve a extraerlo y compilarlo.
7. **Resumen y reinicio.** Lista lo que no se completó y ofrece reiniciar OozeShell.

## Qué actualiza

- El repositorio de los dotfiles, con tus configuraciones y los zips de OozeShell y OozeWalls.
- Los paquetes base que falten y, si lo eliges, todo el sistema (solo Arch).
- El núcleo nativo de OozeShell, que se recompila cuando cambia OozeShell o Qt. La versión de Qt solo se compara en Arch; en NixOS, después de actualizar el sistema, recompílalo a mano (ver [Problemas frecuentes](#problemas-frecuentes)).
- Los enlaces de configuración, los permisos de los scripts y fontconfig.
- Los ajustes de entorno en `env.lua`.
- OozeWalls: solo se recompila si `OozeWalls.zip` cambió y ya lo tenías instalado.

En Arch **no se instala nada de Nix**. Solo se añaden aplicaciones y lo necesario para compilar (por ejemplo `cmake`, `pkgconf` y `rust`).

### Dónde vive cada cosa

| Parte | Origen | Dónde se deja |
|---|---|---|
| OozeShell | `~/dotfiles/OozeShell.zip` | `~/dotfiles/.config/quickshell/OozeShell` |
| Núcleo nativo | dentro de OozeShell | `~/.local/share/oozeshell/qml/OozeShell/Native` |
| OozeWalls (código) | `~/dotfiles/OozeWalls.zip` | `~/.local/share/oozewalls/src` |
| OozeWalls (programas) | compilado desde ese código | `~/.local/bin` |
| Estado del actualizador | huellas de los zips y versión de Qt | `~/.local/share/oozeshell/update-state` |

## Tus cambios están a salvo

- Tus ediciones locales a los dotfiles se apartan en un stash de git y se vuelven a aplicar después de la actualización.
- El OozeShell anterior se respalda en `~/.local/share/oozeshell/backups`.
- `env.lua` se respalda ahí mismo antes de modificarse.
- Los archivos existentes que no sean enlaces simbólicos nunca se reemplazan sin preguntar.
- El código de OozeWalls se reextrae sin tocar su caché de compilación (`target`), así que las recompilaciones siguientes son más rápidas.

## Después de actualizar

- Si el resumen dice que `env.lua` cambió, cierra sesión y vuelve a entrar.
- Si el resumen dice que el propio actualizador cambió, ejecútalo una vez más para usar los pasos nuevos.
- Si dice que el instalador cambió, lee las diferencias si dependes de sus preguntas.

## Si vienes de Oozenix

Los scripts se llamaban `install-oozenix.sh` y `update-oozenix.sh`; ahora son `install-oozedots.sh` y `update-oozedots.sh`. La carpeta sigue siendo `~/dotfiles`, así que tus enlaces no cambian.

La primera actualización con el script viejo trae los nombres nuevos y, al terminar, puede avisar de que no encuentra `install-oozenix.sh` y saltarse la revisión de paquetes y enlaces. También puede decir que el actualizador cambió y pedirte ejecutarlo otra vez: hazlo con el nombre nuevo. Es inofensivo, esa actualización solo renombra archivos.

## Si antes tenías OozeWalls como carpeta

Antes OozeWalls era una carpeta `~/dotfiles/OozeWalls`; ahora es `OozeWalls.zip`. En la primera actualización con el zip, el actualizador no sabe qué versión tenías, así que pregunta una vez si quieres recompilarlo. Responde que sí. Después ya compara las huellas del zip y solo pregunta cuando cambia.

Si la carpeta vieja `~/dotfiles/OozeWalls` quedó en tu disco después de actualizar, puedes borrarla: ya no se usa.

## Problemas frecuentes

**«No existing install detected»**
Ejecuta primero `install-oozedots.sh`. El actualizador solo funciona sobre una instalación existente.

**«The current branch has no upstream»**
Define uno con:
```bash
git -C ~/dotfiles branch --set-upstream-to=origin/main
```

**Tus cambios locales chocan con la actualización**
El repositorio queda limpio en la versión nueva y tus cambios se quedan en el stash. Revísalos y vuelve a aplicarlos con:
```bash
git -C ~/dotfiles stash show -p
git -C ~/dotfiles stash pop
```

**Tu rama divergió del remoto**
El actualizador pregunta si quieres hacer rebase de tus commits locales. Si dices que no, resuélvelo a mano con `git -C ~/dotfiles pull --rebase`.

**El núcleo nativo falla tras actualizar NixOS**
El actualizador no detecta cambios de Qt fuera de Arch. Recompílalo con el Qt actual:
```bash
nix-shell ~/.config/quickshell/OozeShell/native/shell.nix --run 'bash ~/.config/quickshell/OozeShell/Tools/build-native.sh'
```

**El núcleo nativo no compiló**
OozeShell sigue funcionando en QML puro. Comprueba que `cmake` y `pkgconf` estén instalados y reintenta con la ruta del script de compilación que muestra la salida.

**OozeWalls no compiló**
Comprueba que `cargo --version` funciona y reintenta con:
```bash
bash ~/.local/share/oozewalls/src/install.sh
```

**«OozeWalls.zip not found»**
Pon `OozeWalls.zip` en `~/dotfiles`. Mientras no esté, el motor instalado se deja como está.

**No se actualizaron los paquetes del AUR**
Con `-y`, los paquetes del AUR se omiten. Ejecuta tú mismo `paru -Sua --devel` o `yay -Sua --devel`.

**Hyprland es anterior a la 0.55**
La configuración Lua necesita Hyprland 0.55 o superior. Haz una actualización del sistema con `--system`.

## Qué vigilar o conviene cambiar

Las funciones comunes con el instalador (`BASE`, `config_items`, `check_hyprland`, `migrate_ipc_binds`, `tachidesk`) están en [INSTALLER.md](INSTALLER.md#qué-vigilar-o-conviene-cambiar). Estas son propias del actualizador:

| Función | Estado | Por qué |
| --- | --- | --- |
| `nothing_to_do` y el bloque `--check` de `main` | Vigilar | Deciden si hay algo que hacer y repiten las mismas condiciones. Si añades algo que pueda cambiar (otro zip, otra comprobación), súmalo en los dos sitios o el actualizador dirá «Already up to date» sin mirarlo. |
| `unstash_local` | Vigilar | Es el único punto con `git reset --hard`. Es seguro porque `git stash pop` conserva el stash cuando hay conflicto, así que tus cambios siguen ahí. Si lo cambias por otra forma de reaplicarlos, revisa que el stash no se borre antes de tiempo. |
| `save_autogen` y `restore_autogen` | Vigilar | Dependen de la ruta `.config/hypr/modules/appearance/autogen` y de las marcas `skip-worktree` que pone el instalador. Si cambia la ruta, cámbiala en los dos scripts. |
| `qt_version` | Conviene cambiar | Solo consulta `qt6-base` con pacman. En NixOS `qt_changed` nunca es verdadero y el núcleo nativo solo se recompila cuando cambia el shell. Sería mejor leer la versión de Qt con la que se compiló y compararla con la de `quickshell`. |
| `update_packages` y `link_configs` | Vigilar | Leen sus listas del instalador por su formato exacto. Si no las encuentran, avisan y omiten la revisión. |
