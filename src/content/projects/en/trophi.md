---
title: "Trophi"
description: "All your Steam achievements in one place."
cover: "https://images.unsplash.com/photo-1661347561879-c9ab77bac89f?q=80&w=1170&auto=format"
tags: ["Node/Express", "React", "MongoDB", "Docker"]
featured: true
links:
  - label: "GitHub"
    url: "https://github.com/thaishdz/Trophi"
    icon: "simple-icons:github"
toc: "center"
---

## Description

Track your Steam achievements and add notes, guides and more to reach 100% in your games.

![Trophi dashboard](https://raw.githubusercontent.com/thaishdz/Trophi/main/docs/screenshots/2026-11-18.png)

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

A monorepo with separate client and API that runs as a whole with Docker Compose.

## Next steps

- **Deploy a demo.** The session cookie needs adjusting first (`sameSite` and `secure`), along with a long, random `SESSION_SECRET` ([ADR-0012](https://github.com/thaishdz/Trophi/blob/main/docs/adr/en/0012-sessions_in_mongodb.md)).
- **Email and password login.** The `AuthMethod` collection is already designed to hold several login methods per user ([ADR-0001](https://github.com/thaishdz/Trophi/blob/main/docs/adr/en/0001-auth_method_separate_collection.md)).

## Architecture decisions (ADRs)

The [Steam Web API](https://steamcommunity.com/dev) has a daily request limit, so most decisions revolve around caching: each piece of data expires based on how often it changes (library 8 h, progress 30 min), and the achievement list is only fetched again when a new one shows up. The [ADR index](https://github.com/thaishdz/Trophi/blob/main/docs/adr/en/README.md) groups them by topic.

### Steam: quota and caching

- [ADR-0002: Steam achievement sync cache strategy](https://github.com/thaishdz/Trophi/blob/main/docs/adr/en/0002-steam_achievement_sync_cache_strategy.md)
- [ADR-0004: Games library cache refresh](https://github.com/thaishdz/Trophi/blob/main/docs/adr/en/0004-games_library_cache_refresh.md)
- [ADR-0011: Achievements cache and manual refresh](https://github.com/thaishdz/Trophi/blob/main/docs/adr/en/0011-achievements_cache_and_manual_refresh.md)

### Identity and authentication

- [ADR-0001: Auth method separate collection](https://github.com/thaishdz/Trophi/blob/main/docs/adr/en/0001-auth_method_separate_collection.md)
- [ADR-0003: Steam ID middleware](https://github.com/thaishdz/Trophi/blob/main/docs/adr/en/0003-steamid_middleware.md)
- [ADR-0012: Sessions in MongoDB](https://github.com/thaishdz/Trophi/blob/main/docs/adr/en/0012-sessions_in_mongodb.md)

### User data

- [ADR-0006: Saved games](https://github.com/thaishdz/Trophi/blob/main/docs/adr/en/0006-saved_games.md)
- [ADR-0007: Followed achievements in MongoDB](https://github.com/thaishdz/Trophi/blob/main/docs/adr/en/0007-followed_achievements_in_mongodb.md)

### Interface

- [ADR-0005: Game covers from the Steam CDN](https://github.com/thaishdz/Trophi/blob/main/docs/adr/en/0005-game_covers_steam_cdn.md)
- [ADR-0008: Dark visual direction and game tint](https://github.com/thaishdz/Trophi/blob/main/docs/adr/en/0008-dark_visual_direction_and_game_tint.md)
- [ADR-0009: Search results in a `<dialog>` modal](https://github.com/thaishdz/Trophi/blob/main/docs/adr/en/0009-search_results_in_dialog_modal.md)

### Architecture

- [ADR-0010: Inversion of control discarded](https://github.com/thaishdz/Trophi/blob/main/docs/adr/en/0010-inversion_of_control_discarded.md)

## Flows

- [Steam Auth](https://github.com/thaishdz/Trophi/blob/main/docs/flows/steam-auth-flow.md) (in Spanish)
