# OozeShell

Shell hecho con Quickshell y Qt6 que reúne barra, lanzador, notificaciones, bloqueo, fondos, dock y paneles del sistema.

## Modos y estilos

* **Barra**: arriba, abajo, izquierda o derecha; opcionalmente flotante.
* **Islas**: sin fondo de barra; los menús crecen desde su isla.
* **Pill + Dashboard**: píldora compacta que abre un panel completo.
* Estilos **OozeSoft** y **CoOzey**, en claro y oscuro, más algunas paletas extra.

## Funciones principales

* Barra de tareas, bandeja, workspaces, reloj, privacidad, batería, brillo y **Dock**.
* **Dashboard**: resumen, rendimiento, audio, calendario, medios, energía.
* **Launcher** (apps, ventanas, ejecutar, archivos, calculadora, tmux) y **Overview** con vistas previas en vivo.
* **Búsqueda de paquetes**: `nix-search` en NixOS; pacman + AUR en Arch (`Ctrl+L` instalados, `Ctrl+R` desinstalar).
* Notificaciones con No molestar, **Agenda**, historial del portapapeles.
* **Selector de fondos** estáticos y de video, fuentes en línea y paleta con Matugen.
* Capturas y grabación de pantalla.
* Medios (MPRIS), audio y enrutado **OozeAudio**, red, Bluetooth y perfiles de energía.
* **OozeLock**: bloqueo nativo (`ext-session-lock-v1` + PAM), reemplaza a `hyprlock`.
* OSD, menú de energía, editor de monitores, hoja de atajos.
* **Configuración avanzada** con registro con búsqueda.
* Chat opcional con agente de IA (APIs, CLI instaladas u `ollama`).
* Idiomas: English, Español.

## Atajos por defecto

| Teclas | Acción |
| --- | --- |
| `SUPER+I` | Hoja de atajos |
| `SUPER+L` | Selector de monitor |
| `SUPER+G` | Selector de idioma |
| `SUPER+U` | Búsqueda de paquetes |

Fuente de verdad: `hypr/modules/input/keybinds.lua`.

## Comprobación tras editar el shell

```bash
cd ~/.config/quickshell/OozeShell
bash Tools/check.sh
```

## IPC

Permite controlar el shell desde atajos o scripts:

```bash
quickshell ipc -c OozeShell call <objetivo> <función> [argumentos]
```

Comando corto: `oozeshell list` lista los objetivos; `oozeshell apps open browser` es un ejemplo.

| Objetivo | Funciones |
| --- | --- |
| `bar` | `position top\|bottom\|left\|right`, `togglePosition`, `floating true\|false`, `toggleFloating`, `islands`, `pill`, `togglePill`, `get` |
| `corners` | `toggle`, `set true\|false`, `get` |
| `settings` | `toggle`, `open`, `close`, `get` |
| `theme` | `mode light\|dark`, `toggle`, `style cozy\|soft`, `styleGet`, `softfont` |
| `appearance` | `toggle`, `get`, `set <clave> <valor>` |
| `launcher` | `toggle`, `open <apps\|windows\|run\|files>`, `close` |
| `nixsearch` | `toggle`, `open <consulta>`, `installed` (Arch), `close` |
| `overview`, `agenda`, `powermenu`, `clipboard` | `toggle`, `open`, `close` |
| `menu`, `network`, `bluetooth`, `mpris`, `keybinds` | `toggle` |
| `audio` | `toggle`, `raise`, `lower`, `mute`, `micToggle`, `micRaise`, `micLower` |
| `notify` | `toggle`, `show <texto>`, `toggleDnd`, `enableDnd`, `disableDnd`, `isDnd`, `clear` |
| `toggleWalls` | `handle` |
| `screenshot` | `areaCopy`, `areaSave`, `fullSave` |
| `record` | `toggleArea`, `toggleScreen`, `startArea`, `startScreen`, `stop`, `pause`, `resume`, `togglePause`, `cancel`, `status` |
| `lock` | `lock`, `locked` |
| `monitor` | `set <nombre\|auto>`, `get`, `current`, `list`, `togglePicker` |
| `lang` | `set <código>`, `get`, `list`, `togglePicker` |
| `keyboardlayout` | `next` |
| `caps`, `layoutosd` | `show` |

`nixsearch` es el nombre del IPC en todos los sistemas; en Arch no hay nada de Nix detrás.
