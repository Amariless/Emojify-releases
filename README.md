<p align="center">
  <img src="docs/icon.png" width="96" alt="Icono de Emojify">
</p>

<h1 align="center">Emojify</h1>

<p align="center">
  Un selector de emojis para KDE Plasma que escribe el emoji donde estabas escribiendo.
</p>

<p align="center">
  <a href="https://github.com/Amariless/Emojify-releases/releases/latest"><b>Descargar la última versión</b></a>
  &nbsp;·&nbsp; Linux con KDE Plasma 6
</p>

<p align="center">
  <img src="docs/captura.png" width="640" alt="Emojify buscando corazón sobre el escritorio">
</p>

## Qué es

Pulsas **Meta+.**, escribes lo que buscas y presionas Enter: el emoji aparece en la ventana donde estabas, sea WhatsApp Web, Discord, Telegram, el navegador o la terminal. Sin copiar y pegar a mano.

- **Abre al instante** y empieza mostrando tus recientes.
- **Busca como hablas.** Entiende español, español latinoamericano e inglés a la vez ("corazón" o "heart"), sin importar tildes ni mayúsculas, y aunque escribas con errores.
- **Todo con el teclado.** Flechas para moverte, Enter para insertar, Esc para cerrar.
- **Tonos de piel, banderas y favoritos**, y oculta los emojis que tu fuente no puede mostrar.
- **Varios seguidos.** Puedes dejarla abierta e ir insertando emojis mientras sigues escribiendo en el chat.
- **Funciona sin internet.** Cuando quieras, `emojify --update` descarga los emojis más nuevos.
- **Respeta tu portapapeles.** Lo que tenías copiado vuelve a su lugar después de insertar.
- La interfaz está en español, inglés, portugués, francés, alemán e italiano.

## Instalación

Descarga `emojify-<versión>-linux.tar.gz` desde [Releases](https://github.com/Amariless/Emojify-releases/releases/latest), descomprímelo y ejecuta el instalador:

```bash
tar xzf emojify-0.3.0-linux.tar.gz
cd emojify-0.3.0-linux
./install.sh
```

El instalador usa el gestor de paquetes de tu distribución (`pacman`, `apt`, `dnf` o `zypper`) para lo que falte, configura el atajo en KDE y deja Emojify listo para usar. Solo pide la contraseña si hace falta instalar algo del sistema. Al final, pulsa **Meta+.** y pruébalo.

Para desinstalar: `./uninstall.sh` (con `--purge` también borra tus ajustes e historial).

### Dónde funciona

| | |
|---|---|
| Arch, CachyOS, EndeavourOS, Manjaro | Probado en CachyOS con Plasma 6 en Wayland |
| Fedora KDE, openSUSE Tumbleweed, Kubuntu 24.10+, KDE neon, Debian 13 | Debería funcionar; el instalador soporta sus gestores de paquetes |
| Plasma 5, GNOME, Hyprland y otros escritorios | No funciona: depende de KWin, el gestor de ventanas de Plasma 6 |
| Windows y macOS | No. Windows ya trae su propio selector con Win+. |

Requiere Python 3.11 o más nuevo.

## Uso

| Tecla | Acción |
|---|---|
| **Meta+.** | Abrir o cerrar (se puede cambiar) |
| Escribir | Buscar, sin tener que hacer clic |
| Flechas | Moverte por los emojis |
| **Enter** | Insertar |
| **Shift+Enter** | Insertar sin cerrar (o al revés, según tus ajustes) |
| Espacio, con la búsqueda vacía | Elegir el tono de piel de ese emoji |
| Ctrl+D | Añadir o quitar de favoritos |
| Tab | Pasar entre recientes, favoritos y categorías |
| Ctrl+, | Ajustes |
| Esc | Borrar la búsqueda o cerrar |

Con el botón del pin puedes arrastrar la ventana a donde prefieras y se abrirá ahí. Cada pantalla recuerda su posición.

En los ajustes puedes cambiar el atajo, los idiomas de búsqueda, el tono de piel por defecto, el tamaño de la cuadrícula, el tema y si la ventana se cierra al elegir. También se pueden editar en `~/.config/emojify/config.toml`.

<details>
<summary><b>Si algo no funciona</b></summary>

**El atajo no hace nada.** Ejecuta `emojify --setup` y revisa en *Ajustes del sistema → Atajos → KWin* que "Emojify" tenga Meta+. y que nada más lo use.

**Se abre, pero el emoji no se pega y llega una notificación.** El servicio que envía el pegado no está corriendo. Revisa `systemctl --user status ydotool.service` o vuelve a ejecutar `./install.sh`. Mientras tanto, el emoji queda copiado y puedes pegarlo con Ctrl+V.

**En una app concreta se pega tarde o se pega lo que tenías copiado antes.** Sube `paste_delay_ms` en los ajustes (por ejemplo a 80). Si es una terminal que no reconoce, añade su clase de ventana a `terminals`.

**Al borrar una bandera en la barra de direcciones de Chrome o Brave queda una letra suelta.** No es Emojify: una bandera son dos letras especiales y la barra de Chromium las borra de a una. En cuadros de texto normales se borra completa.

**Registros:** `journalctl --user -u emojify -f`

</details>

<details>
<summary><b>Cómo inserta el emoji</b></summary>

En Plasma Wayland ninguna app puede escribir texto directamente en otra ventana. Emojify usa el portapapeles sin que lo notes: guarda lo que tenías copiado, copia el emoji (sin que quede en el historial de Klipper), devuelve el foco a tu ventana, envía Ctrl+V (o Ctrl+Shift+V en terminales) y restaura lo que tenías. Todo pasa en unos milisegundos. Si algo falla, el emoji queda copiado y recibes una notificación: nunca falla en silencio.

Para esto instala un pequeño script de KWin y una regla de ventana, que puedes ver en los ajustes del sistema. `emojify --unsetup` los quita y devuelve Meta+. al selector de Plasma.

</details>

## Créditos

- Icono: [Sofyan Rofiq](https://www.flaticon.es/autores/sofyan-rofiq) en Flaticon.
- Datos de emojis: [Emojibase](https://emojibase.dev) y [Unicode CLDR](https://cldr.unicode.org).
- Emojis: Noto Color Emoji, de Google.

Gratis para uso personal. Los términos completos están en [LICENSE](LICENSE).
