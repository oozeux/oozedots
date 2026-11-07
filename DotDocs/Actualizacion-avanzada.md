# Actualización avanzada

Resumen en el [README](../README.md). Guía completa: [UPDATING.md](../UPDATING.md).

## Qué hace `update-oozedots.sh`

1. Lista los commits nuevos; aparta tus cambios locales (stash), actualiza y los reaplica. Si la rama divergió, ofrece rebase.
2. En Arch: opcionalmente actualiza el sistema, instala paquetes nuevos y refresca `tachidesk`.
3. Extrae `OozeShell.zip` si cambió (respaldo en `~/.local/share/oozeshell/backups/`) y recompila el núcleo nativo si cambió el shell o Qt.
4. Revisa enlaces y permisos, migra llamadas IPC antiguas de tus atajos (con respaldo) y reaplica `env.lua`.
5. Si `OozeWalls.zip` cambió y ya lo tenías, lo recompila; después ofrece reiniciar OozeShell.

## Opciones

| Opción | Efecto |
| --- | --- |
| `--check` | Solo informa si hay actualización. |
| `--system` | También actualiza el sistema (Arch). |
| `--force` | Repite extracción, compilación y revisión de enlaces. |
| `--replace` / `--keep` | Respalda y reemplaza, o conserva, archivos reales que bloquean un enlace. |
| `-y`, `--yes` | No pregunta. |

## Actualización manual

Revisa con `git status && git diff` y elige:

* **Conservar cambios:** commit o `git stash`, luego `git pull` (y `git stash pop`).
* **Descartar un archivo:** `git checkout -- <archivo>`, luego `git pull`.
* **Descartar todo lo local** (borra lo no confirmado): `git fetch origin && git reset --hard origin/main`.

Después ejecuta `bash install-oozedots.sh`: reemplaza la carpeta de OozeShell (con respaldo), ignora `hypr/modules/appearance/autogen` y, en Arch, reaplica `env.lua`.

## Desde la versión previa a OozeDots

```bash
git clone https://github.com/oozeux/oozedots.git ~/dotfiles_new
mv ~/dotfiles ~/dotfiles_old
mv ~/dotfiles_new ~/dotfiles
```

Los scripts antes de Oozenix ahora son `install-oozedots.sh` y `update-oozedots.sh`; la carpeta sigue siendo `~/dotfiles`.
