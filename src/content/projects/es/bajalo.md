---
title: "Bajalo"
description: "Descarga vídeos y audio desde la terminal con una CLI o una TUI, usando yt-dlp por debajo."
pubDate: 2026-10-21
cover: "https://images.unsplash.com/photo-1611162616475-46b635cb6868?q=80&w=1074&auto=format&fit=crop&ixlib=rb-4.1.0&ixid=NHwxMjA3fDA%3D"

tags: ["Python", "Textual", "yt-dlp"]
featured: true
links:
  - label: "GitHub"
    url: "https://github.com/thaishdz/bajalo"
    icon: "simple-icons:github"
toc: "center"
---

## Overview

Bajalo es una herramienta para descargar vídeos y audio desde la terminal. Por debajo usa [yt-dlp](https://github.com/yt-dlp/yt-dlp), así que funciona con cualquier web que este soporte (YouTube, Instagram y muchas más).

![Bajalo TUI tras una descarga completada](/src/content/projects/es/_assets/bajalo-tui.svg)

yt-dlp es muy potente, pero para algo tan habitual como "bájame este vídeo en 1080p y en mp4" obliga a recordar flags y formatos. Bajalo envuelve esa complejidad en dos interfaces que solo te preguntan lo justo:

- **`bajalo`** (o `bj`): una CLI interactiva que hace unas pocas preguntas y descarga.
- **`bajalo-tui`** (o `bjt`): una interfaz de terminal a pantalla completa con formulario, barra de progreso y temas.

El stack:

- **Lenguaje**: Python 3.11+
- **Descargas**: yt-dlp + ffmpeg (para unir vídeo y audio y para convertir audio)
- **TUI**: Textual
- **CLI**: questionary
- **Calidad**: pytest + Ruff
- **CI**: GitHub Actions
- **Distribución**: pipx

## Objetivos

- **Descargar sin memorizar flags**, eligiendo tipo (vídeo o solo audio), carpeta, calidad y formato desde menús.
- **Archivos que se reproduzcan en cualquier sitio**, priorizando códecs compatibles cuando se pide mp4.
- **Funcionar también con vídeos verticales** (reels, Shorts), no solo con los horizontales.
- **Una sola lógica de descarga para dos interfaces**, de modo que la CLI y la TUI no dupliquen código.
- **Instalación en un comando** y sin ensuciar el Python del sistema.

## Cómo funciona

### TUI

```bash
bjt
```

1. Pegas la URL (se valida al momento).
2. Eliges **vídeo** o **solo audio** y dónde guardarlo
3. Ajustas las opciones y click en **Download**.


### CLI

```bash
bj
```

Hace las mismas preguntas paso a paso. Si cancelas con `Ctrl+C`, basta con volver a lanzarla con la misma URL para reanudar la descarga.

### Opciones

| Tipo  | Opción  | Valores                                 |
|-------|---------|-----------------------------------------|
| Vídeo | Calidad | 1080p, 720p                             |
| Vídeo | Formato | auto, mp4, mkv, webm                    |
| Audio | Formato | auto (calidad original), mp3, m4a, opus |

## Decisiones técnicas

- **Núcleo compartido**: toda la descarga vive en `downloader.py` (`download_video` y `download_audio`). La CLI y la TUI solo recogen opciones y llaman a esas funciones; la TUI además se engancha a los `progress_hooks` de yt-dlp para pintar la barra de progreso.
- **Calidad medida por el lado corto**: se ordenan los formatos con la clave `res` de yt-dlp, que mide el lado corto del vídeo. Así "1080p" funciona igual para un vídeo horizontal que para uno vertical.
- **mp4 compatible antes que máxima resolución**: al elegir mp4 se priorizan H.264 y AAC, aunque eso suponga una resolución algo menor, para que el archivo se reproduzca en cualquier dispositivo. Quien quiera la máxima calidad puede elegir **auto**.
- **Audio sin descargar el vídeo entero**: en modo audio solo se pide la pista de audio (`bestaudio`) y ffmpeg la convierte al formato elegido.
- **La UI nunca se bloquea**: la descarga, el selector de carpetas y la apertura del gestor de archivos corren en hilos aparte con los workers de Textual, y actualizan la interfaz con `call_from_thread`.
- **Integración con cada sistema operativo**: el selector de carpetas usa Finder en macOS, el diálogo de .NET en Windows y `zenity`/`kdialog` en Linux. Para mostrar el archivo descargado se usa `open -R`, `explorer /select` o la interfaz D-Bus de freedesktop. Si no hay nada de eso disponible (por ejemplo, sin entorno gráfico), se cae a escribir la ruta a mano.
- **Recuerda tus preferencias**: el tema y la última carpeta usada se guardan en `~/.config/bajalo/config.json` y se restauran al abrir la TUI.

## Calidad y CI

El proyecto tiene tests para las cuatro piezas (CLI, TUI, descargador y utilidades). Los de la TUI usan el modo headless de Textual para simular interacciones reales con el formulario.

En cada push y pull request, GitHub Actions ejecuta en Python 3.11 y 3.14:

- `ruff check` (lint)
- `ruff format --check` (formato)
- `pytest`

## Instalación

```bash
pipx install git+https://github.com/thaishdz/bajalo.git
```

Requiere [ffmpeg](https://ffmpeg.org/) instalado (`brew install ffmpeg` en macOS).

> [!NOTE]
> Bajalo se desarrolla y prueba en **macOS**. Incluye soporte para Linux y Windows, pero aún no está probado en esos sistemas.
