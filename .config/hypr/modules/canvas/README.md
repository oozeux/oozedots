# Infinite Canvas en este dotfiles

Versión adaptada de `infinite-canvas-hyprland` (Asterrooid, MIT) a
`hyprland.lua` + módulos, con el paneo reescrito en Lua puro.

- **Sin evdev, sin `python-evdev`, sin grupo `input`, sin root.**
- El paneo sólo consume CPU mientras arrastras (`hl.timer` de 16 ms).
- El único proceso persistente es `canvas_daemon.py` (socket2): auto-captura
  de ventanas nuevas en modo canvas. No lee `/dev/input`.

## Binds (`modules/canvas/canvas.lua`)

| Bind | Acción |
| :--- | :--- |
| `ALT + botón medio` (arrastrar) | **Pan del canvas** |
| `ALT + rueda arriba/abajo` | Zoom focal in/out (centrado en el cursor) |
| `ALT + 0` | Reset de zoom (585×520, recortado al monitor) |
| `SUPER + ALT + D` | Toggle tiled ↔ canvas |
| `SUPER + ALT + F` | **Fit**: encuadrar todas las tarjetas |
| `SUPER + ALT + H` | **Centrar** la ventana activa |
| `SUPER + ALT + G` | **Tidy**: grid ordenado por app |
| `SUPER + CTRL + flechas` | Navegar y centrar la siguiente tarjeta |
| `SUPER + ALT + flechas` | Mover la tarjeta flotante 90 px |
| `SUPER + CTRL + SHIFT + flechas` | Resize de la ventana |
| `SUPER + ALT + SHIFT + flechas` | Mover ventana en layout tiled |

Los binds usan `ALT` pero **no chocan con las teclas de Hyprland**: no hay
ningún bind solo-ALT en el resto de la config. `ALT+MMB` se consume, así que
no llega a las apps (nadie usa ese combo creo); `ALT+clic izquierdo` se deja libre
a propósito para no romper multi-cursor, "descargar enlace", etc.

## Ajustes rápidos

En `canvas.lua`:

| Constante | Default | Qué hace |
| :--- | :--- | :--- |
| `PAN_BUTTON` | `"mouse:274"` | Botón de paneo: `272` izq., `274` medio, `275` atrás |
| `PAN_SPEED` | `1.0` | Multiplicador de velocidad |
| `PAN_INVERT` | `false` | Invertir la dirección |
| `EDGE_WRAP` | `false` | `true` = al llegar al borde, el cursor salta al lado opuesto y el paneo sigue (infinito real, con salto visible del puntero) |
| `PAN_TICK_MS` | `16` | Frecuencia del paneo (~60 Hz) |

## Cómo funciona

- `floating_tile_toggle.py` convierte el workspace en tarjetas flotantes
  (layout guardado en `$XDG_RUNTIME_DIR/hypr_infinite_state.json`) o vuelve
  al layout tiled (`scrolling`).
- El pan mueve las ventanas flotantes del workspace activo con
  `hl.dsp.window.move({ relative = true })`, sin procesos externos.
- `canvas_daemon.py` escucha el socket2: al abrir una ventana en modo canvas
  la hace flotante y la coloca en el cursor.
- `canvas_fit.py`, `canvas_center.py` y `canvas_tidy.py` sólo se ejecutan
  cuando pulses su bind.

Nota: el "zoom" de este canvas **redimensiona ventanas**, no es una cámara
real (eso requeriría un plugin de compositor tipo Phantomat o Infinity Land).
Por eso el fit también reajusta el tamaño de las tarjetas.

## Requisitos (NixOS)

Nada especial: `python3` y `hyprctl`.
