---
title: "TrophyLogger"
description: "Consulta y organiza tus logros de Steam en un solo lugar."
cover: "https://images.unsplash.com/photo-1661347561879-c9ab77bac89f?q=80&w=1170&auto=format"
tags: ["Node/Express", "React", "Docker"]
featured: true
links:
  - label: "GitHub"
    url: "https://github.com/thaishdz/TrophyLogger"
    icon: "simple-icons:github"
toc: "center"
---

# Overview

TrophyLogger centraliza tus logros de Steam en un solo lugar.

![trophylogger-dashboard](/src/content/projects/es/_assets/trophylogger-dashboard.jpg)

- Backend: Node + Express, TypeScript
- Frontend: React, TypeScript
- Contenerización: Docker
- API: Steam API
- Arquitectura monorepo con `client/` y `server/` separados

## Roadmap

| Area | Status |
| --- | --- |
| Dashboard Design | Done |
| Steam API Integration | Done |
| Auth (OpenID 2.0) | Done |
| Database | In Progress |
| Deployment | Pending |

## Decisiones arquitectónicas (ADRs)

- [ADR-0001: Auth method separate collection](https://github.com/thaishdz/TrophyLogger/blob/main/docs/adr/0001-auth_method_separate_collection.md)
- [ADR-0002: Steam achievement sync cache strategy](https://github.com/thaishdz/TrophyLogger/blob/main/docs/adr/0002-steam_achievement_sync_cache_strategy.md)

## Flujos
- [Steam Auth](https://github.com/thaishdz/TrophyLogger/blob/main/docs/flows/steam-auth-flow.md)
