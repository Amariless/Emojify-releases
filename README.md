# Emojify

Selector de emojis para **KDE Plasma** (probado en CachyOS, Plasma 6.8, Wayland) que se abre con
**Meta+.** y **inserta el emoji directamente** en la ventana que tenía el foco: WhatsApp Web, Discord,
Telegram, el navegador, la terminal… Sin copiar y pegar a mano.

- Abre al instante: la app queda residente y el atajo lo atiende KWin.
- Muestra tus **recientes** al abrir.
- Escribes y busca: el campo siempre tiene el foco. Busca en **español, español latinoamericano e inglés
  a la vez** ("corazón" = "heart"), sin importar mayúsculas ni tildes y con tolerancia a errores ("corzon").
- Flechas para moverte, Enter para insertar, Esc para cerrar.
- Tonos de piel, secuencias ZWJ y banderas. Oculta lo que tu fuente no puede dibujar.
- Funciona 100 % sin conexión; `emojify --update` descarga datos nuevos cuando quieras.

## Descarga

Descarga la última versión desde [Releases](https://github.com/Amariless/Emojify-releases/releases/latest)
(`emojify-<versión>-linux.tar.gz`) y descomprímela:

```bash
tar xzf emojify-0.3.0-linux.tar.gz
```

Requiere KDE Plasma 6 y Python 3.11–3.14. La release trae el programa compilado para cada una de esas
versiones de Python. Solo Linux: Emojify depende de KWin, y Windows ya trae su propio selector con Win+.

## Instalación

Dentro de la carpeta descomprimida:


```bash
./install.sh
```

Qué hace:

1. Instala lo que falte con el gestor de tu distro (`pacman`, `apt`, `dnf` o `zypper`; pide sudo solo si
   hace falta): PySide6, ydotool, libnotify, kwriteconfig6, Noto Color Emoji y python-evdev. Si tu distro
   no empaqueta PySide6, lo instala con pip en un entorno virtual. En ese caso funciona igual, pero sin el
   tema de KDE ni el desenfoque.
2. Si tu usuario no puede escribir en `/dev/uinput`, crea `/etc/udev/rules.d/60-emojify-uinput.rules`
   (etiqueta `uaccess`: solo el usuario de la sesión activa tiene acceso, no hace falta el grupo `input`).
3. Copia la app a `~/.local/share/emojify/app` y crea `~/.local/bin/emojify`.
4. Instala el servicio systemd de usuario `emojify.service`, la activación por D-Bus, el `.desktop` y el icono.
5. Activa `ydotool.service` de usuario (Arch lo trae; en otras distros crea `emojify-ydotoold.service`) y
   `emojify.service`.
6. Ejecuta `emojify --setup` (script de KWin, atajo y conflicto con el selector de Plasma).

Para desinstalar: `./uninstall.sh` (añade `--purge` para borrar también ajustes e historial).


## Uso

| Tecla | Acción |
|---|---|
| **Meta+.** | Abrir / cerrar el selector (configurable) |
| Escribir | Buscar (no hace falta hacer clic) |
| ← ↑ → ↓, RePág/AvPág | Moverse por la cuadrícula, incluso mientras escribes |
| **Enter** / clic | Insertar el emoji al instante (y cerrar, si "Cerrar la ventana" está activado) |
| **Shift+Enter** / Shift+clic | Lo contrario: insertar sin cerrar, o insertar y cerrar |
| Esc | Limpiar la búsqueda o cerrar (en ese orden) |
| Tab / Shift+Tab, **Shift+← / Shift+→** | Pestaña anterior / siguiente (Recientes, Favoritos, categorías) |
| **Espacio** (búsqueda vacía) | Elegir el tono de piel del emoji seleccionado: aparece el mismo emoji en cada tono; ← → y Enter, o clic. El tono elegido se recuerda para ese emoji |
| Ctrl+D o clic derecho | Añadir/quitar de favoritos (clic derecho: copiar, copiar nombre…) |
| Ctrl+0 … Ctrl+5 | Tono de piel: amarillo, claro … oscuro |
| Ctrl+, | Ajustes |

**Modo "seguir abierta"** (`close_on_select = false`) funciona de dos maneras:

- **Con el teclado en el buscador**: cada Enter inserta al instante y el teclado vuelve solo al buscador,
  así que puedes poner varios seguidos, moverte con las flechas o buscar otro. Para pegar, el chat tiene el
  foco unos 60 ms. Mientras tanto Emojify **retiene el teclado físico**: las teclas que pulses en ese
  instante, por ejemplo otro Enter, no llegan al chat (no se envía el mensaje); se aplican al buscador
  justo después. Para esto hace falta `python-evdev` y estar en el grupo `input`.
- **Como un teclado en pantalla**: si haces clic en el chat para seguir escribiendo, el selector deja de
  pedir el foco, mediante una regla de KWin. Cada clic en un emoji se inserta en el chat sin quitarle el
  foco, así que puedes escribir y poner emojis a la vez. Para volver a buscar, haz clic en el buscador o
  pulsa Meta+..

Se cierra con ✕, con Esc (con la búsqueda vacía) o con Meta+.. En el modo normal se cierra sola al perder
el foco.

**Posición**: por defecto aparece sobre la bandeja del sistema, en la esquina del panel de la pantalla
activa. El botón de **pin** (se ve clavado cuando está activo) la fija: arrástrala por cualquier zona vacía y se abrirá ahí. **Cada pantalla
recuerda su propia posición**; en una pantalla donde todavía no la fijaste, aparece sobre la bandeja. Con
clic derecho en el pin eliges "Sobre la bandeja", "Centrada" o "Encima del ratón".

**Idioma**: la interfaz está en español, inglés, portugués, francés, alemán e italiano. En el primer
arranque se usa el idioma del sistema, y también se eligen los idiomas de búsqueda según el sistema
(por ejemplo `es_CO` → `es`, `es-419` y `en`). Se cambia en Ajustes → General → Idioma.

Línea de comandos:

```text
emojify              alternar el selector (arranca el servicio si no está corriendo)
emojify --settings   ajustes
emojify --update     buscar datos nuevos de emojis (--force para descargar igual)
emojify --setup      (re)configurar la integración con KDE
emojify --unsetup    quitar la integración con KDE y devolver Meta+. a Plasma
emojify --quit       cerrar la instancia residente
```

## Cómo se inserta el emoji (y por qué así)

En Plasma Wayland **ninguna app puede "escribir" texto Unicode en otra ventana**. ydotool y el portal
RemoteDesktop/libei solo envían códigos de teclas físicas, no caracteres. La única vía sin portapapeles sería
un método de entrada (fcitx5/ibus), que reemplaza tu configuración de teclado y no funciona bien en
Chromium/Electron sin flags. Emojify usa el portapapeles de forma transparente:

1. Al abrir, guarda una copia de lo que tenías en el portapapeles.
2. Al elegir, pone el emoji en el portapapeles, marcado para que **Klipper no lo guarde en el historial**.
3. Espera a que sueltes Enter (con el clic no hay espera), así la tecla nunca llega al chat.
4. Oculta la ventana, o en modo "seguir abierta" le pide a KWin que devuelva el foco al chat. El script de
   KWin avisa en cuanto la ventana del chat recibe el foco.
5. Envía **Ctrl+V** con `ydotoold` en ese mismo instante, o **Ctrl+Shift+V** en terminales, con 20 ms de
   margen porque Konsole ignora un pegado que llega junto con el foco.
6. En modo "seguir abierta", 60 ms después de que la app leyó el emoji le devuelve el foco al buscador.
7. Al terminar, **restaura lo que tenías copiado**.

Medido en esta máquina: entre soltar Enter y que la app recibe el emoji pasan entre 4 y 35 ms.

Si algo falla (ydotoold caído, o la ventana destino no aceptó el pegado), el emoji queda en el portapapeles
y aparece una notificación. Nunca falla en silencio.

Probado en: apps Qt nativas de Wayland, apps XWayland, Konsole, Brave/Chromium y Firefox. También lo
usaste en WhatsApp Web y Discord.

## Qué toca en KDE

- **Script de KWin** `~/.local/share/kwin/scripts/emojify` (activado en `kwinrc` → `[Plugins] emojifyEnabled`).
  Registra el atajo, coloca y enfoca la ventana (en Wayland las apps no pueden posicionarse solas),
  devuelve el foco al chat en modo "seguir abierta" y avisa cuándo vuelve el foco. Emojify le manda
  órdenes a través del título de su ventana, porque un script de KWin no puede recibir llamadas D-Bus.
- **Regla de ventana** "Emojify: no robar el foco al hacer clic" en `kwinrulesrc`. Solo afecta al selector
  cuando su título contiene `|nofocus|` (modo pasivo). La ves en *Ajustes del sistema → Reglas de ventana*;
  `emojify --unsetup` la quita.
  Lo ves en *Ajustes del sistema → Gestión de ventanas → Scripts de KWin*.
- **Atajo**: queda en *Ajustes del sistema → Atajos → KWin → "Emojify: abrir el selector de emojis"*.
  Puedes cambiarlo ahí o en los ajustes de Emojify; los dos se mantienen sincronizados.
- **Conflicto con el selector de Plasma**: el *Selector de emojis* de Plasma (`plasma-emojier`) usa
  Meta+. por defecto. La primera vez, Emojify **le quita solo Meta+.** y le deja
  **Meta+Ctrl+Alt+Shift+Espacio**, así puedes seguir abriéndolo. `emojify --unsetup` lo deja como estaba.
  Si eliges un atajo que usa otro programa, se le quita a ese programa y se avisa con una notificación.

## Ajustes

Desde la ventana de ajustes (botón ⚙, `Ctrl+,` o `emojify --settings`) o editando
`~/.config/emojify/config.toml`. En la ventana, los cambios se guardan con **Aplicar** o **Aceptar**.
**Cancelar** los descarta, y al cerrar con ✕ pregunta si quieres guardarlos. Cada sección tiene un botón
**Restablecer**. Si editas `config.toml` a mano, los cambios se aplican solos al guardar el archivo.

| Sección | Clave | Descripción |
|---|---|---|
| general | `ui_language` | Idioma de la interfaz: `es`, `en`, `pt`, `fr`, `de`, `it` (vacío = el del sistema) |
| | `shortcut` | Atajo global (`"Meta+."`, `"Ctrl+Alt+E"`…) |
| | `open_view` | Al abrir: `recents`, `favorites`, `last_category` |
| | `languages` | Idiomas de búsqueda; el primero se usa para los nombres |
| | `skin_tone` | 0 amarillo, 1 claro, 2 claro medio, 3 medio, 4 oscuro medio, 5 oscuro |
| | `close_on_select` | Cerrar la ventana al elegir (`false` = sigue abierta y cada emoji se inserta al instante) |
| | `shift_inverts` | Shift hace lo contrario de `close_on_select` |
| | `recents_max` | Cuántos recientes guardar |
| insertion | `method` | `paste` (insertar en la ventana) o `clipboard` (solo copiar) |
| | `keep_in_clipboard` | Dejar el emoji copiado (`false` = restaurar lo que había antes) |
| | `paste_delay_ms` | Espera extra antes de pegar (0 por defecto; las terminales esperan al menos 20 ms) |
| | `terminals` | Clases de ventana que pegan con Ctrl+Shift+V |
| window | `position` | `tray` (sobre la bandeja), `center`, `cursor` (encima del ratón) |
| | `pinned` | Abrir donde la dejaste al arrastrarla (botón de pin); una posición por pantalla, guardada en `state.json` |
| | `show_in_taskbar` | Mostrar el icono en la barra de tareas mientras está abierta |
| | `theme` | `system`, `light`, `dark` (por defecto sigue el esquema y el acento de Plasma) |
| | `emoji_size`, `columns`, `rows` | Tamaño de la cuadrícula |
| | `animations`, `blur` | Animación al abrir y fondo translúcido con desenfoque de KWin |
| system | `autostart`, `tray_icon`, `check_updates` | Inicio con la sesión, icono en la bandeja (desactivado por defecto), búsqueda diaria de datos nuevos |

Historial y favoritos: `~/.local/share/emojify/state.json`. El botón *Borrar historial* está en Ajustes → General.

## Datos e idiomas

- Fuentes: [Emojibase](https://emojibase.dev) (estructura, categorías, tonos, nombres `en`/`es`) y
  [Unicode CLDR](https://cldr.unicode.org) (anotaciones `es-419`).
- La app incluye una copia (`emojify/resources/emojis.json.gz`). `emojify --update` descarga la versión
  más reciente, **la valida** y recién ahí reemplaza `~/.local/share/emojify/emojis.json.gz`. Un fallo de red
  o datos inválidos nunca tocan la copia que funciona.
- Con `check_updates = true` revisa una vez al día, en segundo plano, si hay una versión nueva de Emojibase o CLDR.

**Añadir un idioma de la interfaz**: copia `emojify/locales/en.json` a `<código>.json`, traduce los
valores (las claves son el texto original en español) y añade el código a `UI_LANGUAGES` en
`emojify/i18n.py`. `tests/test_i18n.py` avisa si falta algún texto o algún marcador `{...}`.

**Añadir un idioma de búsqueda**: márcalo en Ajustes → General → Idiomas, o añádelo a `languages` en `config.toml`
(p. ej. `["es", "es-419", "en", "pt"]`). Emojify descarga lo que falte automáticamente. Puedes usar cualquier
código de Emojibase (`pt`, `fr`, `de`, `it`, `ja`…) o de CLDR (`ca`, `gl`, `eu`, `pt-PT`…). Los subidiomas
heredan del idioma padre (`es-419` → `es`).

## Compatibilidad

| | Estado |
|---|---|
| Arch, CachyOS, EndeavourOS, Manjaro | Probado en CachyOS (Plasma 6.8, Wayland). `install.sh` o PKGBUILD |
| Fedora KDE (40+), openSUSE Tumbleweed, Kubuntu 24.10+, KDE neon, Debian 13 | `install.sh` instala los paquetes con `dnf`, `zypper` o `apt` (sin probar) |
| Plasma 6 en X11 | Debería funcionar: KWin, ydotool y el portapapeles se comportan igual (sin probar) |
| Plasma 5 (Debian 12, Kubuntu 22.04/24.04) | No: el script de KWin usa la API de Plasma 6 |
| GNOME, Sway, Hyprland, otros | No: el atajo, la posición y el foco dependen del script de KWin |
| Windows / macOS | No: todo depende de KWin. Windows ya trae su selector con Win+. |

Requisitos: Python ≥ 3.11, PySide6 ≥ 6.5, ydotool ≥ 1.0 (la versión 0.1.x de Debian 12 no sirve),
`kwriteconfig6`, `dbus-send` y una fuente de emojis a color. Opcional: `python-evdev`.

## Solución de problemas

**El atajo no hace nada.** Revisa que el script esté activo:

```bash
qdbus6 org.kde.KWin /Scripting org.kde.kwin.Scripting.isScriptLoaded emojify
```

Si da `false`, ejecuta `emojify --setup`. Comprueba también en *Ajustes del sistema → Atajos → KWin* que
"Emojify" tenga Meta+. y que nada más lo use.

**Se abre pero el emoji no se pega (llega una notificación).** ydotoold no está corriendo o no tiene acceso
a `/dev/uinput`:

```bash
systemctl --user status ydotool.service
```

```bash
ls -l /dev/uinput
```

Vuelve a ejecutar `./install.sh` para crear la regla udev. Mientras tanto, el emoji queda en el portapapeles.

**En una app concreta se pega tarde o no se pega.** Sube `paste_delay_ms` (por ejemplo a 80). Si es una
terminal que no está en la lista, añade su clase de ventana a `terminals`. Para ver la clase: *Ajustes del
sistema → Gestión de ventanas → Reglas de ventana → Detectar propiedades de ventana*.

**Al borrar una bandera en la barra de direcciones de Brave/Chrome queda una letra (🇨🇴 → 🇨).** No es
Emojify: una bandera son dos "letras regionales" y el omnibox de Chromium las borra de a una. Pasa igual
si pegas la bandera a mano. En los cuadros de texto normales se borra entera.

**Se pega lo que tenía copiado antes.** La app destino tardó más de lo normal en leer el portapapeles. Sube
`paste_delay_ms` o activa `keep_in_clipboard`.

**Wayland vs X11.** Está hecho y probado para Plasma Wayland. En Plasma X11 funciona igual: KWin, ydotool y
el portapapeles se comportan igual, aunque no lo probé. Fuera de KDE no funcionan el atajo ni la posición,
porque dependen del script de KWin.

**Registros** (con `EMOJIFY_DEBUG=1` en el servicio se ven los tiempos de cada paso):

```bash
journalctl --user -u emojify -f
```


## Créditos

- Icono de la app: [Sofyan Rofiq](https://www.flaticon.es/autores/sofyan-rofiq) (Flaticon).
- Datos: [Emojibase](https://emojibase.dev) y [Unicode CLDR](https://cldr.unicode.org).
- Emojis: Noto Color Emoji (Google).
