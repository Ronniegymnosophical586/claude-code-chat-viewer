<p align="center">
  <a href="https://hitmman55.github.io/claude-code-chat-viewer/">
    <img src="https://img.shields.io/badge/▶-PRUÉBALO%20ONLINE-2ea043?style=for-the-badge" alt="Pruébalo online" />
  </a>
</p>
<p align="center"><i>Solo haz clic — sin instalación, sin compilación, sin registro.</i></p>

<p align="center">
  <a href="README.md">English</a> ·
  <a href="README.ru.md">Русский</a> ·
  <b>Español</b> ·
  <a href="README.fr.md">Français</a> ·
  <a href="README.zh-CN.md">中文</a>  ·
  <a href="README.ar.md">العربية</a>
</p>

# Claude Code Chat Viewer

<p align="center">
  <img src="https://img.shields.io/badge/license-Unlicense-blue" alt="Licencia: Unlicense" />
  <img src="https://img.shields.io/badge/runtime%20deps-0-brightgreen" alt="Cero dependencias en runtime" />
  <img src="https://img.shields.io/badge/offline-first-success" alt="Funciona offline" />
  <img src="https://img.shields.io/badge/i18n-6%20languages-informational" alt="6 idiomas de interfaz" />
</p>

<p align="center">
  <img src="screenshot.png" alt="Captura de pantalla" width="900" />
</p>

Visor HTML para transcripciones de sesiones de [Claude Code](https://claude.com/claude-code) en formato JSONL. Se abre en el navegador — sin servidor, sin build, una sola dependencia empaquetada localmente. Funciona offline sin configuración.

## Por qué

Claude Code guarda cada sesión en `~/.claude/projects/<project>/<session-uuid>.jsonl` — una línea por registro (mensaje del usuario, respuesta del modelo, thinking, tool_use, tool_result, attachment, etc.). El archivo sin procesar es ilegible; comandos integrados como `/resume` muestran la conversación pero no permiten exportarla ni revisarla después.

Este visor convierte ese `.jsonl` en un feed legible con colores por rol, bloques de servicio plegables (thinking / tools / results) y filtros.

## Qué muestra

- **user** (azul) — mensajes reales del usuario
- **assistant** (verde) — respuestas de texto de Claude
- **thinking** (morado) — razonamiento extendido, plegado por defecto
- **tool_use** (ámbar) — llamadas a herramientas con vista previa de argumentos
- **tool_result** (cian / rojo para errores) — respuestas de herramientas
- **meta / task-note** (amarillo) — inyecciones del sistema y `<task-notification>` de subagentes
- **system / attachment / ui-state** — registros de servicio (ocultos por defecto)

Cada bloque es una fila independiente con una barra coloreada a la izquierda. Sin burbujas de mensajería: esto es un log, no un chat.

## Cómo abrirlo

1. Clona el repo o descarga el ZIP. Necesitas `index.html` y la carpeta `lib/`.
2. Doble clic sobre `index.html` — se abre en cualquier navegador moderno.
3. Usa el selector de archivo y elige una transcripción `.jsonl`.

Las transcripciones de Claude Code están en:

```
~/.claude/projects/<project-slug>/<session-uuid>.jsonl
```

donde `<project-slug>` es tu directorio de trabajo con `/` reemplazado por `-`. Ejemplo: `/home/user/myproj` → `-home-user-myproj`.

## Características

- **Tema claro / oscuro** — botón en la cabecera, la preferencia se guarda en `localStorage`.
- **Seis idiomas de interfaz** — English, Русский, Español, Français, 中文, العربية. El árabe cambia a RTL. Selector en la cabecera, preferencia guardada.
- **Parseo en streaming** — `.jsonl` se lee vía `file.stream()` + `TextDecoderStream`, no se carga como una sola cadena.
- **Virtualización nativa** — `content-visibility: auto` en cada registro: el navegador omite layout y paint para entradas fuera de pantalla. Escala a miles de registros.
- **Renderizado por lotes** — 500 registros por lote, botón «Mostrar más» para el resto.
- **Filtros** — cinco casillas (thinking / tools / results / system / ui-state), alternan categorías vía una sola clase CSS en el contenedor (sin reflow del DOM).
- **Renderizado seguro frente a XSS** — cada bloque de texto se escapa _antes_ de pasar por el parser de markdown. Ningún HTML crudo de la transcripción llega al DOM, por lo que no se necesita sanitizador en tiempo de ejecución.
- **Límites de tamaño** — bloques de prosa truncados a 20 KB, bloques de servicio a 5 KB (suelen llevar 10-50 KB de ruido que nadie lee). La serialización también está acotada — entradas de herramientas grandes no se materializan enteras antes del recorte.
- **Fallback para `.json`** — si el archivo no es JSONL sino un array/objeto JSON plano, se interpreta como lista de registros.

## Requisitos del navegador

- Chrome / Edge 85+
- Safari 18+
- Firefox 125+

Todo ello para `content-visibility: auto`. En navegadores antiguos el visor se abre, pero el scroll en archivos grandes será notablemente más lento.

## Dependencias

Una sola, empaquetada localmente en `lib/`:

- [marked](https://github.com/markedjs/marked) — markdown → HTML (~35 KB)

Sin CDN, sin red, sin Subresource Integrity. Clona y ejecuta.

## Privacidad

Todo funciona localmente en tu navegador. El visor en sí **no realiza peticiones de red automáticas** — sin CDN, sin analítica, sin fuentes remotas. Las imágenes Markdown incrustadas en las transcripciones se neutralizan: se muestran como texto inerte con la URL visible, pero nunca se descargan. Los enlaces externos (sólo `http(s)`) se abren en una nueva pestaña sólo al hacer clic, con `rel="noopener noreferrer nofollow"`. Tus transcripciones se quedan en tu máquina.

## Limitaciones conocidas

- Archivos mayores de ~100 MB necesitan carga indexada por line-offsets con renderizado por ventanas (no implementado).
- No hay exportación a Markdown/HTML (el objetivo es visualizar, no convertir).
- Sin resaltado de sintaxis en bloques de código (decisión consciente para minimizar dependencias).

## Desarrollo

Todo el código vive en un único archivo HTML. Edítalo directamente — estilos en `<style>`, lógica en `<script>`, traducciones en el objeto `I18N` al principio del script.

Verificar la sintaxis de JS sin navegador:

```bash
sed -n '/^<script>$/,/^<\/script>$/p' index.html | sed '1d;$d' | node --check /dev/stdin
```

## Licencia

[Unlicense](LICENSE) — dominio público. Úsalo como quieras, no se requiere atribución.
