# Roadmap — openLostSaga

Handoff document for the implementing agent. Read every section before
starting. Work through milestones in order — do **not** jump to
deployment before M1 and M2 are done.

## 0. Project context

**Goal:** recreate *Lost Saga* (2.5D beat-'em-up PvP) as an open source,
browser-playable, multiplayer game.

**Local reference materials (never commit these to the repo):**
`/home/anoe/agent-do/app-reverse/LS_extracted/` — 16 GB extraction of the
publicly circulated 2014 Lost Saga C++/DirectX9 source tree + DB dumps.

⚠️ **Legal rules (HARD):**
- No Lost Saga source, assets, textures, or models may enter this repo.
- Allowed: **reading** mechanics from the original source (damage numbers,
  combo timings, state machines), then **reimplementing them cleanly in TS**.
- Character assets: create our own, or use open-source sets
  (Kenney, Quaternius, Sketchfab CC-BY).
- Code license: MIT. Self-made assets: CC-BY.

**Key reference paths inside LS_extracted:**

| Path | Contents |
|---|---|
| `src/src/LSClient/ioBaseChar.h/.cpp` | Character state machine: combos, jump, defense, buffs, wounded stagger |
| `src/src/LSClient/ioPlayStage.h/.cpp` | Arena/stage logic, gravity, drop-out |
| `src/src/LSClient/TCPProtocol.h` (2,853 lines) | Packet IDs: lobby/room/items/account |
| `src/src/LSClient/P2PProtocol.h` (361 lines) | Packet IDs: real-time gameplay (attack, jump, skills, sync) |
| `src/src/ls_gamesvr/` | Server code (server-authoritative validation reference) |
| `src/src/io3DEngine/` | Original render engine (mesh, animation, anim events) |
| `src/src/LSClient/Channeling/` + skill/weapon classes (~880 classes) | Skill & weapon system data |
| `DBs/LosaGame_B.sql` | Game DB schema + stored procedures |
| https://github.com/LSFDC | Community org; GPL-3.0 Blender plugin for `.skl/.msh/.ani` formats |

## 1. Tech stack (fixed — swap only when blocked)

- **Language:** strict TypeScript (frontend & backend)
- **Build:** Vite (client) + Bun/Node 20+ (server)
- **Render:** Three.js (WebGL2)
- **Netcode:** WebSocket; binary Float32Array for transforms, JSON for chat/config
- **Loop:** fixed 60 Hz timestep for gameplay logic, interpolation for render
- **DB:** SQLite (dev) → Cloudflare D1 (prod)
- **Deploy:** Cloudflare Pages + Workers + Durable Objects (all free tier)

## 2. Milestone M1 — Local gameplay core (solo, offline)

- [ ] Scaffold: `apps/client` (Vite+TS+Three.js), `apps/server`, `packages/protocol`, `packages/core`
- [ ] pnpm workspace + strict tsconfig + ESLint + Prettier
- [ ] `packages/core`: fixed 60 Hz timestep loop (logic fully separate from render)
- [ ] 2.5D scene: ground plane, side-view camera, lights — first character visible
- [ ] `CharacterState` entity: pos, vel, facing, state
- [ ] State machine: Walk / Dash / Jump / DoubleJump / ComboAttacks / Defense
- [ ] Combo system: repeated attack input advances combo index; timings from skill data
- [ ] Hit detection: per-frame AABB hitboxes (XY plane, 2.5D)
- [ ] Dummy target: HP, damage, knockback, respawn
- [ ] Debug overlay (F3): FPS, active state, hitbox wireframes
- [ ] Gameplay data in `packages/core/data/*.json` from day one
      (Showdown-style data-driven design — no hardcoded balance numbers)
- [ ] Repo structure follows Ruffle's pattern: `packages/core` is 100%
      platform-agnostic — NO imports of Three.js or browser APIs;
      rendering/adapters live separately

**Definition of done:** a capsule/primitive character can walk, jump, dash
and combo in the browser; a dummy takes combo damage with knockback, dies
and respawns; all gameplay logic lives in `packages/core`.

**Skills:** `test-driven-development`, `systematic-debugging`, `verification-before-completion`.

## 3. Milestone M2 — Local multiplayer (2 clients + 1 server)

- [ ] `packages/protocol`: packet enums + codec (ArrayBuffer encode/decode)
- [ ] Bun_WS server: room joins, player slots, 20 Hz snapshot broadcast
- [ ] Clients send **inputs, never positions**; remote players interpolated
- [ ] Client-side prediction + simple server reconciliation
- [ ] Two-tab test: hits validated on the **server**
- [ ] Reconnect/disconnect handling (3 s timeout → drop)
- [ ] Desync detection: state hash every N ticks + `docs/desync.md`
      (OpenTTD lesson — they maintain a dedicated desync debugging doc)

❌ **Netcode hard rule:** client-sent positions = cheat vector by design.
The server is authoritative and validates hits. `ls_gamesvr/ioPacketChecker.*`
is the mental reference.

**Skills:** `using-git-worktrees`, `subagent-driven-development`.

## 4. Milestone M3 — Free deployment (all free tier)

- [ ] Cloudflare Pages for the static client
- [ ] Workers + Durable Objects for realtime (1 DO per room, ~8 players)
- [ ] D1 for simple accounts (username-only first, no passwords)
- [ ] CI: GitHub Actions (lint + typecheck + test on every push)
- [ ] Deploy → Playwright e2e: two browser contexts fighting on the production URL
- [ ] Automated nightly/demo builds via CI (Ruffle lesson: online demo +
      nightly releases let contributors play without cloning the repo)
- [ ] **Option B (Colyseus pivot):** if matchmaking grows complex
      (global room lists, queues, >8 players), pivot the server to
      **Colyseus** (Node.js, MIT, server-authoritative, built-in matchmaking)
      hosted on Fly.io. Tradeoff: less free than Cloudflare. Evaluate ONLY
      if hand-rolled Durable Objects hit their limits.

**Skills:** `wrangler` / `cloudflare`, `verification-before-completion`.

## 5. Milestone M4 — Feel like Lost Saga

Extract the actual numbers from the reference first — never invent them.

- [ ] Wounded stagger tiers (weak/mid/strong) — values from `ioBaseChar.cpp`
- [ ] Grab/Throw when close to an enemy (see `CUPK_*` handlers)
- [ ] Hitfall / knock-off-the-platform (the signature Lost Saga mechanic)
- [ ] Two arenas + Deathmatch mode (1v1 & 2v2)
- [ ] Two heroes, each with one active skill
- [ ] HUD: HP bars, combo counter, name tags
- [ ] SFX (optional)

## 6. Skills for the builder agent (superpowers)

| Skill | When |
|---|---|
| `brainstorming` | before designing netcode API & data schema |
| `writing-plans` / `executing-plans` | milestone breakdown into tasks |
| `test-driven-development` | ALL gameplay logic + protocol codec |
| `using-git-worktrees` | risky netcode/feature experiments |
| `systematic-debugging` | any bug |
| `subagent-driven-development` / `dispatching-parallel-agents` | independent tasks (asset parser vs state machine) |
| `verification-before-completion` | before every commit/deploy/"done" |
| `requesting-code-review` | end of each milestone |
| `finishing-a-development-branch` | milestone integration |
| `wrangler`, `cloudflare` | M3 |

## 7. MCP servers

| MCP | Use case |
|---|---|
| **playwright** | REQUIRED for M2/M3 — e2e testing with 2 tabs/browser contexts |
| **github** (optional) | PR/issue management |
| **context7 / docs** (if available) | Three.js & Workers API lookups; fallback to official docs via webfetch |
| **rea** (already installed) | only for binaries/assets unreadable from C++; usually not needed |

## 8. Working conventions

1. Finish ONE milestone completely (all checkboxes) before moving on.
2. Small, focused commits with descriptive messages.
3. **Never copy-paste C++ from Lost Saga.** Read → understand → rewrite in
   TypeScript with a self-consistent architecture (ECS or OOP — pick one, stay consistent).
4. All gameplay numbers go in `packages/core/data/*.json` — data ≠ logic.
5. `.gitignore` — from the first commit: exclude `LS_extracted/`, `LS.rar`, `unrar.log`.
6. Record uncertain design decisions in `docs/decisions.md` — never choose silently.
7. End every session with `pnpm lint && pnpm test && pnpm build` green.
