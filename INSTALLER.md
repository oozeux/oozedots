# Cómo funciona `install-oozedots.sh`

Esta página explica el instalador bloque por bloque, para que puedas leer el
script sin adivinar para qué sirve cada parte. El script solo conserva títulos
cortos de bloque (`# ── Nombre ──`); todo lo demás se explica aquí.

Si solo quieres **usar** el instalador, te basta el [README](README.md#instalación).
Lee esta página si quieres saber qué le hará a tu sistema, arreglar algo o
añadir un paquete.

## Índice

1. [Visión general](#visión-general)
2. [Reglas que se cumplen siempre](#reglas-que-se-cumplen-siempre)
3. [Opciones de línea de comandos](#opciones-de-línea-de-comandos)
4. [Etapa 1 — Preparación](#etapa-1--preparación)
5. [Etapa 2 — Comprobaciones previas](#etapa-2--comprobaciones-previas)
6. [Etapa 3 — Cuestionario y revisión](#etapa-3--cuestionario-y-revisión)
7. [Etapa 4A — Rama de Arch](#etapa-4a--rama-de-arch)
8. [Etapa 4B — Otras distribuciones](#etapa-4b--otras-distribuciones)
9. [Etapa 5 — Enlazado](#etapa-5--enlazado)
10. [Etapa 6 — Cierre y resumen](#etapa-6--cierre-y-resumen)
11. [Compilaciones opcionales](#compilaciones-opcionales)
12. [Paquetes que NO se instalan a propósito](#paquetes-que-no-se-instalan-a-propósito)
13. [Archivos que el instalador puede crear o cambiar](#archivos-que-el-instalador-puede-crear-o-cambiar)
14. [Qué vigilar o conviene cambiar](#qué-vigilar-o-conviene-cambiar)

---

## Visión general

El script se ejecuta de arriba abajo en seis etapas:

```text
1. Preparación     variables y funciones auxiliares
2. Comprobaciones  ¿no eres root? ¿existe ~/dotfiles? ¿está OozeShell.zip? ¿es una actualización?
3. Cuestionario    preguntas, revisión de tus respuestas y confirmación (aún no se cambia nada)
4. Instalación     Arch  → instala paquetes y compila lo opcional
                   Otras → escribe la lista de dependencias (NixOSInstallation.md)
5. Enlazado        enlaces simbólicos, permisos de ejecución, migraciones de configuración
6. Cierre          OozeWalls, env.lua, ajustes de git y resumen final
```

---

## Reglas que se cumplen siempre

* Nunca se ejecuta como root. Llama a `sudo` solo en los pocos comandos que lo necesitan.
* Pregunta antes de hacer algo invasivo (actualizar el sistema, servicios, apps opcionales).
* **No cambia nada hasta que confirmas el resumen** de la Etapa 3.
* Nunca borra tus archivos. Lo que reemplaza se mueve antes a una copia `.bak-<fecha>`.
* Un paquete que falla no detiene la instalación: se anota en una lista y se muestra en el resumen final.
* **En Arch solo se instalan aplicaciones, terminales y lo necesario para compilar** (el núcleo nativo de OozeShell y OozeWalls). No se instala ninguna app de NixOS ni ningún paquete de Nix, y nunca se llama a `nix-shell`.
* `nix-shell` solo se usa en la rama de otras distribuciones (NixOS).

---

## Opciones de línea de comandos

| Opción | Efecto |
| --- | --- |
| `-y`, `--yes` | Acepta la respuesta sugerida a cada pregunta (modo desatendido). |
| `-h`, `--help` | Muestra la ayuda. |

---

## Etapa 1 — Preparación

### Variables

| Variable | Significado |
| --- | --- |
| `DOTFILES` | La carpeta del repositorio. Siempre `~/dotfiles`. |
| `QS_DIR` | Dónde viven las configuraciones de Quickshell dentro del repositorio (`.config/quickshell`). |
| `ZIP_FILE` | `~/dotfiles/OozeShell.zip`. Obligatorio. |
| `OOZEWALLS_ZIP` | `~/dotfiles/OozeWalls.zip`. Opcional: sin él no se pregunta por OozeWalls. |
| `OOZEWALLS_SRC` | Dónde se extrae y compila OozeWalls (`~/.local/share/oozewalls/src`). |
| `STAMP` | Fecha y hora de esta ejecución. Nombra los respaldos para que nunca choquen. |
| `BACKUP_DIR` | Adonde se mueve una copia anterior de OozeShell (`~/.local/share/oozeshell/backups`). |
| `STATE_FILE` | `~/.local/share/oozeshell/update-state`. Guarda la huella (sha256) de los zips instalados; el actualizador la compara para saber si cambiaron. |
| `NATIVE_PREFIX` | Dónde se instala el núcleo nativo (`~/.local/share/oozeshell/qml`). |
| `IS_ARCH`, `IS_UPDATE`, `HAS_NVIDIA`, `INSTALL_LEGACY` | Respuestas sí/no que se reúnen durante la ejecución. |
| `OPT_*` | Una por cada pregunta opcional (`OPT_PARU`, `OPT_UPDATE`, `OPT_SERVICES`, `OPT_WAYBAR`, `OPT_EXTRAS`, `OPT_SUWAYOMI`, `OPT_NATIVE`, `OPT_OOZEWALLS`…). |
| `NATIVE_OK`, `OOZEWALLS_OK`, `SUWAYOMI_OK` | Valen `true` solo si esa parte opcional se instaló de verdad. El resumen final las lee. |
| `CONFLICT_POLICY` | Qué hacer si ya hay un archivo real donde iría un enlace: `ask`, `backup` o `skip`. |
| `AUR_HELPER` | `paru` o `yay` si se encuentra; vacío si no. |
| `FAILED` | Lista de paquetes o pasos que no funcionaron. |

### Funciones auxiliares

| Función | Qué hace |
| --- | --- |
| `ask_yes_no` | Hace una pregunta y repite hasta que la respuesta empieza por `y` o `n`. Con `--yes` usa la sugerida. |
| `ask_choice` | Muestra un menú numerado y devuelve la opción elegida. |
| `resolve_conflict` | Aplica `CONFLICT_POLICY` cuando algo real bloquea un enlace. |
| `sudo_keepalive_start` | Pide la contraseña de `sudo` una vez y la mantiene viva mientras el script corre. |
| `create_symlink` | Enlaza un archivo del repositorio a tu carpeta personal. Lo omite si ya está bien, reemplaza un enlace equivocado y pregunta antes de apartar un archivo real. |
| `oozeshell_tools`, `oozeshell_services` | Encuentran las carpetas `Tools` y `Services` dentro de OozeShell. Los zips antiguos usaban otros nombres (`tools`, y `Services` en la raíz); estas funciones comprueban qué estructura hay, así que los zips viejos y los nuevos se instalan bien. Se llaman después de extraer el zip. |
| `file_hash`, `state_set` | Calculan la huella de un zip y la guardan en `STATE_FILE`. |
| `unzip_to` | Descomprime un zip con `unzip`. Solo si no estás en Arch y falta `unzip`, usa un `nix-shell` temporal. |
| `extract_oozeshell` | Mueve la carpeta `OozeShell` actual a los respaldos, descomprime la nueva, borra cualquier carpeta de compilación nativa vieja y anota la huella del zip. |
| `build_native` | Compila el núcleo nativo en C++ y anota el éxito en `NATIVE_OK`. |
| `maybe_build_native_other` | Lo mismo, para sistemas que no son Arch. Usa `nix-shell` si existe. |
| `extract_oozewalls` | Vacía `OOZEWALLS_SRC` **sin tocar `target`** (la caché de compilación) y descomprime `OozeWalls.zip` ahí. |
| `oozewalls_root` | Devuelve la carpeta que contiene `Cargo.toml` (en la raíz del zip o en una única subcarpeta). |
| `install_oozewalls` | Ofrece compilar el motor de fondos OozeWalls (Rust). |
| `migrate_ipc_binds` | Arregla llamadas IPC antiguas en tus atajos (ver Etapa 5). |

Solo en Arch: `pkg_installed`, `install_pkgs`, `install_first`, `check_hyprland` y las funciones de Suwayomi (ver Etapa 4A).

---

## Etapa 2 — Comprobaciones previas

1. **Comprobación de root.** Se detiene si eres root. Los enlaces acabarían en `/root` y `makepkg` se niega a correr como root de todos modos.
2. **Carpeta de dotfiles.** Se detiene si `~/dotfiles` no existe.
3. **`OozeShell.zip`.** Se detiene si falta `~/dotfiles/OozeShell.zip`.
4. **Detección de actualización.** Si `~/.config/hypr` ya es un enlace al repositorio, el script pasa a *modo actualización* (ver Etapa 6).

---

## Etapa 3 — Cuestionario y revisión

El cuestionario reúne todas las respuestas **antes** de tocar nada. Está dividido
en secciones numeradas (5 en Arch, 4 en otras distribuciones).

| Sección | Qué pregunta | Variable |
| --- | --- | --- |
| *(previa)* | «¿Instalas en Arch Linux?». La sugerencia sale de si existen `/etc/arch-release` y `pacman`. Si dices que sí pero no los encuentra, avisa y pregunta si continúas. | `IS_ARCH` |
| **1. System** | Arch: instalar `paru` (solo si no hay ayudante de AUR) y actualizar todo el sistema. Otras: ofrece un `nix-shell` temporal si falta `unzip`. | `OPT_PARU`, `OPT_UPDATE` |
| **2. Desktop** | Arch: qué terminal, activar servicios y Waybar. Todas: herramientas heredadas (`swaync`, `swayosd`, `wlogout`; fuera de Arch solo se enlazan sus configuraciones). | `TERM_CHOICE`, `OPT_SERVICES`, `OPT_WAYBAR`, `INSTALL_LEGACY` |
| **3. Extras and hardware** *(solo Arch)* | Paquete de extras, Suwayomi (y si se arranca al terminar) y variables de NVIDIA. | `OPT_EXTRAS`, `OPT_SUWAYOMI`, `OPT_SUWAYOMI_START`, `HAS_NVIDIA` |
| **4. Optional builds** | Núcleo nativo de animación y motor OozeWalls (esta pregunta solo aparece si existe `OozeWalls.zip`). | `OPT_NATIVE`, `OPT_OOZEWALLS` |
| **5. Existing files** | Qué hacer si ya existe una configuración real en `~/.config`: preguntar cada vez, respaldar y reemplazar todo, o conservar la tuya. | `CONFLICT_POLICY` |

Después viene la **revisión**: una tabla con todas tus respuestas y la pregunta
final «Start the installation?». Si respondes que no, el script termina sin
haber cambiado nada.

---

## Etapa 4A — Rama de Arch

Se ejecuta en este orden. Cada fila es un bloque del script.

| Bloque | Qué ocurre | ¿Depende de una pregunta? |
| --- | --- | --- |
| **Preparación del sistema** | Si no hay ayudante de AUR y lo aceptaste, instala `paru-bin` con `makepkg`. Si lo aceptaste, ejecuta `sudo pacman -Syu`. | Sí |
| **Ayudantes de paquetes** | Define las funciones de instalación (ver abajo). | No |
| **Ayudantes de Suwayomi** | Define las funciones que configuran el servidor de manga opcional. | No |
| **Paquetes base** | Instala la lista `BASE`; luego `quickshell` y `awww` (versión estable o `-git`, la que exista). | No |
| **Herramientas de portapapeles** | Se asegura de que existan `wl-copy`, `wl-paste` y `cliphist`. | No |
| **Terminal** | Instala la terminal que elegiste (`foot`, `kitty`, `alacritty`, `wezterm` o `ghostty`). | Sí |
| **Servicios** | Activa `NetworkManager`, `bluetooth` y `power-profiles-daemon`. | Sí |
| **Paquetes opcionales** | Waybar y las herramientas heredadas `swaync`, `swayosd`, `wlogout`. | Sí |
| **Extras** | Paquete de desarrollo, juegos y utilidades (ver abajo). | Sí |
| **Suwayomi** | Instala el servidor y el comando `tachidesk`; si no, escribe `SuwayomiSetup.md`. | Sí |
| **Descompresión de OozeShell** | Llama a `extract_oozeshell`. | No |
| **Fuentes** | Copia el respaldo de fontconfig para que los iconos nunca salgan como cuadrados vacíos. | No |
| **Núcleo nativo** | Instala `cmake` y `pkgconf` y compila el módulo C++ (ver [Compilaciones opcionales](#compilaciones-opcionales)). | Sí |

### Las funciones de instalación

| Función | Comportamiento |
| --- | --- |
| `pkg_installed` | Verdadera si pacman ya tiene el paquete. |
| `install_pkgs` | Separa la lista en *repositorio oficial* y *AUR*, instala cada grupo en un lote y **reintenta uno por uno** si el lote falla, para que un nombre malo no bloquee al resto. |
| `install_first` | Recibe alternativas (por ejemplo `quickshell quickshell-git`) e instala la primera que funcione. |
| `check_hyprland` | Avisa si Hyprland es anterior a la 0.55, porque la configuración Lua necesita la 0.55 o superior. |

### Qué contiene `BASE` y por qué

| Grupo | Paquetes | Para qué sirve |
| --- | --- | --- |
| Herramientas de compilación | `base-devel git curl unzip zip` | Compilar paquetes del AUR y el núcleo nativo; descomprimir los zips. |
| Shell y scripts | `zsh jq python` | `.zshrc`, manejo de JSON y `palette-apply.py` (necesita Python 3.11 o superior). |
| Compositor | `hyprland hyprpaper hypridle hyprlock hyprpolkitagent hyprshot hyprsunset hyprshutdown hyprsysteminfo` | El gestor de ventanas y sus ayudantes. |
| Portales | `xdg-desktop-portal` + `-hyprland` + `-gtk` | Compartir pantalla y selectores de archivos. |
| Qt | `qt6-base qt6-declarative qt6-svg qt6-shadertools qt6ct` | Con lo que dibuja Quickshell; también las cabeceras para compilar el núcleo nativo. |
| Audio | `pipewire pipewire-pulse wireplumber libpulse` | Sonido. `libpulse` aporta `pactl`, que usa la grabación de pantalla para elegir el dispositivo de audio. |
| Hardware | `brightnessctl power-profiles-daemon upower` | Brillo, modos de energía y batería. |
| Red y Bluetooth | `networkmanager network-manager-applet bluez bluez-utils blueman` | Menús de Wi-Fi y Bluetooth. |
| Medios | `playerctl mpv mpvpaper ffmpeg socat` | Control de reproductores, fondos en vivo, optimización de video y unión de grabaciones. `mpv` también aporta `libmpv`, necesaria para compilar OozeWalls. |
| Utilidades de escritorio | `xdg-utils xdg-user-dirs libnotify imagemagick wtype wev` | Abrir enlaces, encontrar las carpetas Imágenes y Videos, notificaciones. |
| Captura | `grim slurp wf-recorder` | Capturas de pantalla y grabación. |
| Herramientas de audio | `cava pavucontrol qpwgraph` | Visualizador, control de volumen y grafo de rutas. |
| Varios | `fastfetch rofi tmux matugen` | Información del sistema, lanzador alternativo, sesiones del lanzador y paleta a partir del fondo. |
| Fuentes | JetBrainsMono Nerd Font, Symbols Nerd Font Mono, Noto Color Emoji, Silkscreen, DotGothic16, VT323 | Iconos, emoji y el tema pixel. |

Después de instalar la base, el script ejecuta `xdg-user-dirs-update`, que crea
tus carpetas estándar (Imágenes, Videos…) si aún no existen. Las capturas y las
grabaciones se guardan ahí.

**Para añadir un paquete:** pon su nombre en la lista `BASE` (o en `EXTRAS` para
el paquete opcional). `install_pkgs` averigua solo si está en los repositorios
oficiales o en el AUR. El actualizador lee esta misma lista `BASE`, así que no
hay que tocar nada más.

### El paquete de extras

`greetd greetd-tuigreet udisks2 rtkit flatpak yazi neovim starship fd ripgrep
dolphin nomacs gamemode lutris mangohud wine github-cli jdk21-openjdk lazygit
python-pip tree qt6-quick3d wget vim uv ruff bibata-cursor-theme prismlauncher
protonplus protontricks goverlay vscodium-bin pw-viz`

`greetd` y `rtkit` se instalan pero **no** se configuran ni se activan. Las
herramientas de Nix (`nixd`, `nixfmt`) **no** están en esta lista: no se
instalan en Arch.

---

## Etapa 4B — Otras distribuciones

Con cualquier sistema que no sea Arch, el script lo trata como NixOS y **no
instala ningún paquete**.

1. Descomprime OozeShell. Si falta `unzip` y existe `nix-shell`, usa uno temporal con `unzip`; si no, se detiene y te pide instalar `unzip`.
2. Si lo pediste, compila el núcleo nativo mediante `nix-shell`.
3. Escribe `NixOSInstallation.md` **solo si no existe**: el repositorio ya trae ese archivo, así que normalmente solo lo reutiliza. El texto de respaldo vive dentro del script; mantenlo idéntico al `NixOSInstallation.md` del repositorio.

---

## Etapa 5 — Enlazado

### Enlaces simbólicos

Cada nombre de `config_items` se enlaza desde `~/dotfiles/.config/<nombre>` a
`~/.config/<nombre>`. Enlaces extra: `.zshrc`, `random-fastfetch`, la carpeta de
temas de Vesktop, `nix-rofi` (solo fuera de Arch) y `niri` / `mango` si sus
carpetas existen en el repositorio. El comportamiento cuando el destino ya
existe es el de la tabla del README.

### Permisos de ejecución

Una lista explícita de scripts recibe `chmod +x`. Los archivos que faltan se
informan, pero no detienen la ejecución. Después corre un **barrido de
seguridad** sobre todos los `*.sh` y `*.py` de OozeShell y los hace ejecutables.
Cubre los scripts que se añadan en el futuro y los zips reconstruidos en
sistemas que pierden los bits de permiso.

### Migración de atajos (`migrate_ipc_binds`)

Las versiones antiguas de los dotfiles llamaban a nombres de IPC o rutas que ya
no existen. El script revisa estos archivos y los corrige en su sitio, dejando
una copia `.bak-<fecha>`:

* `hypr/modules/input/keybinds.lua`
* `hypr/modules/startup/programs.lua`
* `niri/modules/binds.kdl`
* `mango/conf/keybinds.conf`

| Forma antigua | Se reemplaza por |
| --- | --- |
| Objetivo IPC `pacsearch` | `nixsearch` (detecta Arch por sí mismo) |
| `.../OozeShell-mango/shell.qml` | `.../OozeShell/shell.qml` |
| `OozeShell call apps ...` con una ruta `/home/<usuario>` fija, sin `shell.qml` y sin `--` | `~/.config/quickshell/OozeShell/shell.qml call -- apps ...` |
| `spawn "qs ipc ..."` de niri y `,spawn,qs ipc ...` de mango | `spawn-sh` y `spawn_shell`, porque `spawn` a secas no ejecuta un shell y `~` nunca se expandiría |

### El comando `oozeshell`

`Tools/oozeshell` se enlaza a `~/.local/bin/oozeshell`. Es una forma corta de
llamar al IPC (`oozeshell apps open browser`). El script avisa si
`~/.local/bin` no está en tu `PATH`.

---

## Etapa 6 — Cierre y resumen

| Bloque | Qué ocurre |
| --- | --- |
| **Motor OozeWalls** | Solo si lo pediste y existe `OozeWalls.zip`. Ver [Compilaciones opcionales](#compilaciones-opcionales). |
| **Arch: env.lua** | Respalda `hypr/modules/system/env.lua`, activa la línea de la sugerencia de Electron y, si dijiste que sí a NVIDIA, las dos líneas de NVIDIA. Funciona quitando el `--` inicial de las líneas comentadas. |
| **Núcleo nativo: QML_IMPORT_PATH** | Si la compilación nativa funcionó, añade una línea a `env.lua` para que Quickshell encuentre el módulo. Tienes que cerrar sesión y volver a entrar una vez. |
| **Modo actualización** | Solo si se detectó una instalación existente. Le dice a git que ignore los cambios locales en `hypr/modules/appearance/autogen`, porque OozeShell regenera esos archivos. Se imprime el comando para deshacerlo. |
| **Resumen** | Lista todo lo que haya en `FAILED`, y luego muestra cómo iniciar OozeShell y qué partes opcionales están activas. |

---

## Compilaciones opcionales

Las dos son opcionales. Cada una viaja como un archivo comprimido en la raíz de
los dotfiles y se pregunta en la sección **Optional builds** del cuestionario.

### Núcleo nativo de OozeShell (C++)

| | |
| --- | --- |
| Origen | Dentro de `OozeShell.zip` (`native/`, compilado con `Tools/build-native.sh`). |
| Destino | `~/.local/share/oozeshell/qml/OozeShell/Native`. |
| Arch | Instala `cmake` y `pkgconf`. El compilador (`base-devel`) y Qt6 (`qt6-base`, `qt6-declarative`, `qt6-shadertools`) ya vienen con las dependencias base. |
| NixOS | Usa `native/shell.nix` mediante `nix-shell`. El Qt del shell debe ser el mismo que usa `quickshell`. |
| Si falla | OozeShell sigue funcionando en QML puro. |

### OozeWalls (Rust)

| | |
| --- | --- |
| Origen | `~/dotfiles/OozeWalls.zip`. |
| Código | Se extrae en `~/.local/share/oozewalls/src`. Si ya había una versión, se vacía esa carpeta **excepto `target`**, para reaprovechar la caché de compilación. |
| Compilación | Se ejecuta `install.sh` desde esa carpeta; este llama a `cargo build --release`. |
| Destino | `oozewalls`, `oozewalls-gui` y `oozewalls-live` en `~/.local/bin`, más el icono y la entrada del lanzador. |
| Arch | Instala `rust` (si falta `cargo`), `pkgconf`, `mpv`, `wayland`, `libxkbcommon`, `mesa`, `libglvnd`, `fontconfig`, `freetype2`, `libx11`, `libxcursor`, `libxrandr`, `libxi`, `xdg-desktop-portal` y `xdg-desktop-portal-gtk`. **No usa Nix**: `install.sh` solo mira `/nix/store` si existe, y en Arch instala los programas directamente. |
| NixOS | Usa `nix-shell -p cargo rustc gcc pkg-config`. `install.sh` resuelve las librerías en `/nix/store` e instala envoltorios que fijan `LD_LIBRARY_PATH`. |
| Si falla | Se anota `oozewalls (build)` en `FAILED`; reintenta con `bash ~/.local/share/oozewalls/src/install.sh`. |

---

## Paquetes que NO se instalan a propósito

| Paquete | Por qué se deja fuera |
| --- | --- |
| `aria2` | Opcional. Acelera las descargas de fondos en línea. Sin él se usa `curl`. |
| `libsecret` + un llavero | Opcional. Guarda las claves de API de los agentes de IA en tu llavero. Sin ellos van a un archivo cifrado. |
| `nvidia-utils` | Viene con tu driver de NVIDIA y aporta `nvidia-smi` para las estadísticas de la GPU. Instalarlo aquí podría chocar con paquetes de drivers antiguos. |
| `niri`, `mango` | Compositores alternativos. OozeShell los detecta si los instalas tú. |
| `nix`, `nix-search`, `nixd`, `nixfmt` | Herramientas de Nix. No tienen sentido en Arch, donde la búsqueda de paquetes usa pacman + AUR. |
| `claude`, `gemini`, `codex`, `ollama` | Motores opcionales para los agentes de IA. Instala los que uses. |

---

## Archivos que el instalador puede crear o cambiar

| Ruta | Cuándo |
| --- | --- |
| `~/.config/<nombre>` | Enlaces simbólicos, siempre. |
| `<destino>.bak-<fecha>` | Había un archivo o carpeta real y dijiste que sí a moverlo. |
| `~/.local/share/oozeshell/backups/OozeShell.bak-<fecha>` | Una copia anterior de OozeShell. |
| `~/.local/share/oozeshell/update-state` | Siempre que se extrae OozeShell o se compila OozeWalls (huellas de los zips). |
| `~/.local/share/oozeshell/qml/OozeShell/Native` | Compilación del núcleo nativo. |
| `~/.local/share/oozewalls/src` | Código de OozeWalls extraído de `OozeWalls.zip` y su caché de compilación. |
| `~/.config/fontconfig/conf.d/99-oozeshell-pixel.conf` | Arch, respaldo de iconos. |
| `~/.config/hypr/modules/system/env.lua` (+ `.bak-<fecha>`) | Arch, y núcleo nativo. |
| `~/.local/bin/oozeshell`, `oozewalls`, `oozewalls-gui`, `oozewalls-live`, `random-fastfetch` | Enlaces y compilaciones. |
| `~/.local/share/applications/oozewalls-gui.desktop` y su icono | Si compilas OozeWalls. |
| `/usr/local/bin/tachidesk` y `~/.config/systemd/user/tachidesk.service` | Solo si instalas Suwayomi. |
| `~/dotfiles/SuwayomiSetup.md` | Solo si omites Suwayomi. |
| `~/dotfiles/NixOSInstallation.md` | Rama de otras distribuciones, solo si el archivo no existe. |

---

## Qué vigilar o conviene cambiar

Cada fila dice si el código **debe mantenerse vigilado** (funciona, pero otra parte depende de su forma exacta) o **conviene cambiarlo** (funciona, pero tiene un punto débil conocido). Las que afectan al actualizador están también en [UPDATING.md](UPDATING.md#qué-vigilar-o-conviene-cambiar).

| Dónde | Estado | Por qué |
| --- | --- | --- |
| `BASE` y `config_items` | Vigilar | El actualizador los lee con `sed` buscando su sangría exacta (`  BASE=(` … `  )` y `config_items=(` … `)`) y separa por espacios. Si los reformateas, los mueves de nivel o metes líneas que no sean nombres, el actualizador deja de revisar paquetes o enlaces y solo muestra un aviso. |
| Texto de respaldo de `NixOSInstallation.md` | Conviene cambiar | Duplica en el script el archivo del repositorio, que siempre viene con él, así que casi nunca se usa. Mientras exista, debe ser idéntico; compruébalo con el comando de abajo. Lo más simple sería quitarlo y mostrar un error si el archivo falta. |
| `extract_oozeshell` | Conviene cambiar | Mueve OozeShell a los respaldos antes de descomprimir y, si `unzip` falla, aborta sin devolverlo a su sitio. La versión del actualizador sí lo restaura; conviene copiarla. |
| Lista `scripts=(...)` | Conviene cambiar | Casi toda es redundante con el barrido de `*.sh` y `*.py`, y envejece cuando se renombra algo dentro de los zips. El actualizador ya barre todo `.config` y podría usarse lo mismo aquí. |
| `migrate_ipc_binds` y los respaldos de `env.lua` | Conviene cambiar | Dejan el `.bak-<fecha>` junto al archivo. Como `~/.config/hypr` es un enlace al repositorio, esas copias suelen acabar dentro de él. El actualizador las guarda en `~/.local/share/oozeshell/backups`; conviene igualar. La función está además duplicada en ambos scripts: si cambias un `sed`, cámbialo en los dos. |
| `check_hyprland` | Vigilar | El mínimo `0.55.0` está escrito en los dos scripts y en los textos de README y UPDATING. Súbelo en todos a la vez. |
| `tachidesk_ctl_body` | Vigilar | El actualizador compara el comando instalado con su propia copia del texto. Si dejan de ser idénticos, ofrecerá «refrescarlo» en cada ejecución. |
| Rutas dentro de los zips | Vigilar | Los scripts dan por hecho `Tools/build-native.sh`, `native/shell.nix`, `Tools/fonts/99-oozeshell-pixel.conf`, `Tools/oozeshell`, `Tools/ooze-launch.sh` y, en OozeWalls, `install.sh` y `Cargo.toml`. Si renombras algo de eso, la mayoría de los pasos se omiten con un aviso, pero `chmod +x` sobre `install.sh` no se comprueba y con `set -e` detendría el script. |
| `oozeshell_tools`, `oozeshell_services` | Conviene cambiar | Las rutas alternativas (`tools`, y `Services` en la raíz) solo existen para zips antiguos. Cuando ya no los soportes, déjalas fijas. |
| `DOTFILES="$HOME/dotfiles"` | Vigilar | Está fijo en los dos scripts, en la detección de instalación existente y en los textos. Cambiar la carpeta rompería las instalaciones actuales, cuyos enlaces apuntan a `~/dotfiles`. Por eso OozeDots conserva esa ruta. |

Comprobar que el texto de respaldo coincide con el archivo del repositorio (sin salida = idénticos):

```bash
sed -n '/cat > "\$NIXOS_MD"/,/^EOF$/p' install-oozedots.sh | sed '1d;$d' | diff - NixOSInstallation.md
```
