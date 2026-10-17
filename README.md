# openLostSaga

An open source, browser-playable, multiplayer reimagining of the classic
2.5D beat-'em-up **Lost Saga** (IO Entertainment, 2009).

- **Open source** — MIT-licensed code, community-made assets
- **Browser-first** — no install; WebGL rendering via Three.js
- **Multiplayer** — real-time matches with an authoritative server

> ⚠️ **Legal note:** This project is a *from-scratch* reimplementation.
> It contains no code, art, audio, or other assets from Lost Saga.
> Original mechanically-inspired gameplay values are reference data only.
> We do not affiliate with, or endorse copying from, any official
> Lost Saga service (KR/TW/CN/Origin). Commercial use of this project is
> allowed under the MIT license, but must not include any original game assets.

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
