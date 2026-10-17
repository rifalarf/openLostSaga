# openLostSaga

An open source, browser-playable, multiplayer reimagining of the classic
2.5D beat-'em-up **Lost Saga** (IO Entertainment, 2009).

- **Open source** — MIT-licensed code, community-made assets
- **Browser-first** — no install; WebGL rendering via Three.js
- **Multiplayer** — real-time matches with an authoritative server

> ⚠️ **Project scope:** openLostSaga is a **direct port** of the publicly
> circulated 2014 Lost Saga source tree, translated to TypeScript/WebGL,
> and bundled with the original game assets. The translated code and the
> original assets remain copyrighted by their original rights holders
> (IO Entertainment / successors); the MIT license in this repo applies
> to the port tooling, build system, and new code written by contributors.
> This project is not affiliated with any official Lost Saga service
> (KR/TW/CN/Origin) and is intended for the lost-game preservation /
> community play.

## Stack

| Layer     | Tech                              |
| --------- | --------------------------------- |
| Client    | TypeScript, Vite, Three.js (WebGL2) |
| Server    | Node.js/Bun, WebSocket, authoritative sim |
| Enums/codec | `@openlostsaga/protocol` (binary ArrayBuffer) |
| Core sim  | `@openlostsaga/core` (fixed 60 Hz timestep, platform-agnostic) |
| State     | SQLite (dev) → Cloudflare D1 (prod) |
| Deploy    | Cloudflare Pages + Workers + Durable Objects (free tier) |

## Roadmap

See [docs/ROADMAP.md](docs/ROADMAP.md) for the full milestone breakdown:
M1 local gameplay core → M2 local multiplayer → M3 free deployment →
M4 "feel like Lost Saga" features (stagger tiers, grab/throw, hitfall).

## Development

```bash
pnpm install
pnpm lint && pnpm test && pnpm build
```

(Quickstart will land with milestone M1.)

## Contributing

Inspired by mature open-source game projects: OpenTTD (desync debugging,
free asset policy), Ruffle (clean core/render split), BrowserQuest
(shared sim code), Colyseus (room + server-authoritative patterns).

Join us — gameplay data lives in plain JSON (`packages/core/data/`),
so balance changes don't require touching code.
