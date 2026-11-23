---
title: "Trophi"
description: "Todos tus logros de Steam en un mismo sitio."
cover: "https://images.unsplash.com/photo-1661347561879-c9ab77bac89f?q=80&w=1170&auto=format"
tags: ["Node/Express", "React", "MongoDB", "Docker"]
featured: true
links:
  - label: "GitHub"
    url: "https://github.com/thaishdz/Trophi"
    icon: "simple-icons:github"
toc: "center"
---

## Descripción

Registra el seguimiento de tus logros de Steam y añade notas, guías y demás para obtener el 100% en tus juegos.

![Dashboard de Trophi](https://raw.githubusercontent.com/thaishdz/Trophi/main/docs/screenshots/2026-11-18.png)

<div class="not-prose my-6 flex flex-wrap gap-1">
  <img src="https://img.shields.io/badge/Node.js-5FA04E?style=flat-square&logo=nodedotjs&logoColor=white" alt="Node.js" />
  <img src="https://img.shields.io/badge/Express-000000?style=flat-square&logo=express&logoColor=white" alt="Express" />
  <img src="https://img.shields.io/badge/TypeScript-3178C6?style=flat-square&logo=typescript&logoColor=white" alt="TypeScript" />
  <img src="https://img.shields.io/badge/Passport-34E27A?style=flat-square&logo=passport&logoColor=black" alt="Passport" />
  <img src="https://img.shields.io/badge/React_19-61DAFB?style=flat-square&logo=react&logoColor=black" alt="React 19" />
  <img src="https://img.shields.io/badge/TanStack_Query-FF4154?style=flat-square&logo=reactquery&logoColor=white" alt="TanStack Query" />
  <img src="https://img.shields.io/badge/Tailwind_CSS-06B6D4?style=flat-square&logo=tailwindcss&logoColor=white" alt="Tailwind CSS" />
  <img src="https://img.shields.io/badge/Vite-646CFF?style=flat-square&logo=vite&logoColor=white" alt="Vite" />
  <img src="https://img.shields.io/badge/MongoDB-47A248?style=flat-square&logo=mongodb&logoColor=white" alt="MongoDB" />
  <img src="https://img.shields.io/badge/Mongoose-880000?style=flat-square&logo=mongoose&logoColor=white" alt="Mongoose" />
  <img src="https://img.shields.io/badge/Docker_Compose-2496ED?style=flat-square&logo=docker&logoColor=white" alt="Docker Compose" />
  <img src="https://img.shields.io/badge/Jest-C21325?style=flat-square&logo=jest&logoColor=white" alt="Jest" />
  <img src="https://img.shields.io/badge/Supertest-444444?style=flat-square" alt="Supertest" />
  <img src="https://img.shields.io/badge/Vitest-6E9F18?style=flat-square&logo=vitest&logoColor=white" alt="Vitest" />
</div>

## Decisiones arquitectónicas (ADRs)

La [Web API de Steam](https://steamcommunity.com/dev) tiene un límite diario de peticiones, así que la mayoría de decisiones giran en torno a la caché: cada dato caduca según lo que cambia (biblioteca 8 h, progreso 30 min) y la lista de logros solo se vuelve a pedir cuando aparece uno nuevo. El [índice de ADRs](https://github.com/thaishdz/Trophi/blob/main/docs/adr/README.md) los agrupa por tema.

### Steam: cuota y caché

- [ADR-0002: Estrategia de caché para sincronizar logros](https://github.com/thaishdz/Trophi/blob/main/docs/adr/0002-steam_achievement_sync_cache_strategy.md)
- [ADR-0004: Caché y refresco de la biblioteca de juegos](https://github.com/thaishdz/Trophi/blob/main/docs/adr/0004-games_library_cache_refresh.md)
- [ADR-0011: Caché de logros y refresco manual](https://github.com/thaishdz/Trophi/blob/main/docs/adr/0011-cache_de_logros_y_refresco_manual.md)

### Identidad y autenticación

- [ADR-0001: Métodos de login en una colección aparte](https://github.com/thaishdz/Trophi/blob/main/docs/adr/0001-auth_method_separate_collection.md)
- [ADR-0003: Middleware para resolver el Steam ID](https://github.com/thaishdz/Trophi/blob/main/docs/adr/0003-steamid_middleware.md)
- [ADR-0012: Sesiones en MongoDB](https://github.com/thaishdz/Trophi/blob/main/docs/adr/0012-sesiones_en_mongodb.md)

### Datos del usuario

- [ADR-0006: Juegos guardados](https://github.com/thaishdz/Trophi/blob/main/docs/adr/0006-juegos_guardados.md)
- [ADR-0007: Seguimiento de logros en MongoDB](https://github.com/thaishdz/Trophi/blob/main/docs/adr/0007-seguimiento_de_logros_en_mongodb.md)

### Interfaz

- [ADR-0005: Portadas desde el CDN de Steam](https://github.com/thaishdz/Trophi/blob/main/docs/adr/0005-game_covers_steam_cdn.md)
- [ADR-0008: Estilo oscuro y tinte por juego](https://github.com/thaishdz/Trophi/blob/main/docs/adr/0008-direccion_visual_oscura_y_tinte_por_juego.md)
- [ADR-0009: Resultados de búsqueda en un `<dialog>` modal](https://github.com/thaishdz/Trophi/blob/main/docs/adr/0009-resultados_de_busqueda_en_dialog_modal.md)

### Arquitectura

- [ADR-0010: Inversión de control descartada](https://github.com/thaishdz/Trophi/blob/main/docs/adr/0010-inversion_de_control_descartada.md)

## Flujos

- [Autenticación con Steam](https://github.com/thaishdz/Trophi/blob/main/docs/flows/steam-auth-flow.md)

## Próximos pasos

- **Desplegar una demo.** Antes hay que ajustar la cookie de sesión (`sameSite` y `secure`) y usar un `SESSION_SECRET` largo y aleatorio ([ADR-0012](https://github.com/thaishdz/Trophi/blob/main/docs/adr/0012-sesiones_en_mongodb.md)).
- **Login con email y contraseña.** La colección `AuthMethod` ya está pensada para tener varios métodos de login por usuario ([ADR-0001](https://github.com/thaishdz/Trophi/blob/main/docs/adr/0001-auth_method_separate_collection.md)).
