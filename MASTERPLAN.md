# Masterplan: an arcade basketball game better than Basketball Stars

> Working title: **Basketball Legends** (rename before launch, see §11).
> Goal: a clean, fast, very fun 2D arcade basketball game in the browser, with real online multiplayer, players whose attributes actually matter, and mechanics that reward skill.

---

## 1. What we're competing with

**Basketball Stars** (MadPuffers, 2019, HTML5, on CrazyGames):

| What it does well | What it lacks (our opening) |
|---|---|
| Opens instantly in the browser and works on mobile | **No online multiplayer.** Two players only means two people sharing one keyboard |
| Short matches with simple controls | **No 3-pointers.** There is almost no shooting skill, so most points come from dunks |
| Big-head characters with a lot of personality | Players feel the same apart from one special ability each |
| A Super Shot meter gives comeback moments | Shots go in or miss mostly by RNG. There's no shot timing, contest system or real ball physics |
| Tournament mode, 1v1 and 2v2 against the CPU | The AI gets cheap on high difficulty, and there's no ranking, progression or reason to come back |
| | Uses real NBA names and likenesses, which is a licensing risk we must **not** copy |

**What we're building:** keep the instant fun and big-head charm, then add depth, fairness, online play and polish.

---

## 2. Design pillars (use these to settle every decision)

1. **Easy to learn, hard to master.** You can play in 10 seconds with 4 buttons. The skill ceiling comes from timing (shot release, block windows, steals), not from button combos.
2. **Every player feels different.** Attributes change how a character moves, shoots and defends. A 5-foot speedster and a 7-foot center should play like different games.
3. **Online play has to feel local.** Rollback netcode makes the game feel instant at up to about 100 ms ping. Nothing else matters if this fails.
4. **Juice everywhere.** Every action gives satisfying feedback: hit-stop on blocks, slow-mo on game-winners, rim shake, crowd roar, screen shake on dunks.
5. **Fair and free.** No pay-to-win. Everything you pay for (if anything) is cosmetic.
6. **Short sessions.** A match lasts 2–3 minutes. "One more game" is the core loop.

---

## 3. Core gameplay

### 3.1 Format
- **Side-view 2.5D court** with a single hoop (half-court streetball). This is simpler to read, better for 1v1, and makes every possession a duel. We can add full-court as a mode later.
- **Match:** first to 21, or a 2-minute clock, with a 12-second shot clock. After a make, possession goes to the other team at the top of the key (check-ball). A defensive rebound has to be cleared past the 3-point line.
- **Scoring:** 2 points inside the arc, 3 outside, and a +1 style bonus for a highlight play (posterizer, ankle-breaker or buzzer-beater) in casual modes only.

### 3.2 Controls (4 actions plus movement)

| Action | Keyboard (P1) | Gamepad | Touch |
|---|---|---|---|
| Move / sprint (double-tap or hold) | A/D (+ Shift) | Left stick (+ RT) | Virtual stick |
| **Shoot / Dunk** (hold and release) | J | X / □ | Button |
| **Jump / Block** | K | A / ✕ | Button |
| **Steal / Crossover** (context) | L | B / ○ | Button |
| **Super** (when the meter is full) | I | Y / △ | Button |
| Pass (2v2) | U | RB | Swipe to teammate |

The same button does different things in different contexts. Nothing needs a manual.

### 3.3 Mechanics (this is where we beat the original)

**Shooting: timed release + arc physics**
- Hold Shoot: the player jumps and a shot meter appears above their head. Release in the green window.
- **Green-window size** = f(shooting stat, distance, contest level, stamina, moving or set).
- The release grade (perfect / good / early / late) sets the launch-angle and velocity error. The ball then flies with **real projectile physics** and interacts with the rim, backboard and net. Makes and misses are emergent, not dice rolls. A "perfect" release is always a make unless the shot is heavily contested.
- **Contest:** the defender's distance and hand position at release shrink the green window. Closing out matters.
- Shot types are picked automatically by context: layup, floater, jumper, step-back (back + shoot), fadeaway, and a hook shot for bigs.

**Dunking: approach + power**
- Hold Shoot while driving inside the dunk radius. The **dunk radius** scales with the Dunk stat and height (bigs dunk from closer, high flyers from the free-throw line on a sprint).
- **Dunk vs. block** is a timing duel. A defender who jumps in the block window, with enough Block + Strength against the dunker's Dunk + Strength, gets a **rejection** (hit-stop, ball flies out of bounds). If they jump too early, the dunker posterizes them.

**Ball handling and defense**
- **Crossover:** tap Steal while dribbling. A successful cross against a defender leaning the wrong way staggers them, and a perfect timing makes it an **ankle-breaker** where the defender falls. Handles stat versus the defender's Lateral Quickness.
- **Steal:** a timing-based reach. A success rate driven by Steal versus Handles is good. Spamming it causes a reach-in **stagger** on yourself, which punishes button mashing.
- **Body contact:** players can't pass through each other. Strength decides who pushes whom in the post and on drives. This gives posting up for bigs and screens in 2v2.
- **Rebounding:** the ball keeps real physics after a miss. Jump timing, height and Rebound stat decide who gets it, and boxing out (standing between the opponent and the rim) gives a bonus.
- **Pump fake:** tap Shoot then cancel with Jump to bait a defender into jumping.

**Stamina**
- Sprinting, dunking and repeated jumps drain stamina. Low stamina shrinks the shot window and slows players. This stops sprint-spam and rewards pacing. It refills during dead balls and while walking.

**Super meter**
- Fills from makes, blocks, steals and highlight plays, plus a small **comeback bonus** when you're trailing.
- Activates the character's **Signature Move** (§4.3) rather than a generic dunk-from-anywhere. Every character's super feels different.

**2v2 extras**
- Passing with soft auto-target, **alley-oops** (pass toward a teammate who's in the air near the rim), **give-and-go**, pick-and-roll screens (thanks to body contact), and switch-on-screen defense.

### 3.4 Game feel checklist
- Responsive acceleration curves, coyote time on jumps, and input buffering (6 frames) on every action.
- Hit-stop of 3–6 frames on blocks, ankle-breakers and dunks.
- Camera: a smooth follow with slight zoom on dunks, and slow-mo with a crowd swell on game-winners.
- Particles: net swish, rim sparks on perfect releases, floor dust on landings, a fire trail when a player is "on fire" (3 makes in a row means a slightly bigger green window, broken by a miss or a block).
- Audio: sneaker squeaks, layered crowd that reacts to score margin, a distinct swish versus rim-in, and announcer barks ("BANG!", "Get that outta here!").

---

## 4. Characters and attributes

### 4.1 Attributes (rated 1–10, shown as bars)

| Attribute | Affects |
|---|---|
| **Speed** | Top speed and sprint acceleration |
| **Agility** | Lateral quickness, crossover recovery and change-of-direction |
| **Vertical** | Jump height, which feeds blocks, rebounds and dunk range |
| **Shooting** | Size of the green window on jumpers and 3s |
| **Finishing** | Layups, floaters and dunk radius |
| **Handles** | Crossover success, steal resistance and dribble speed |
| **Defense** | Steal success, contest strength and block window |
| **Strength** | Pushing in contact, dunk-vs-block duels and box-outs |
| **Stamina** | Size of the stamina pool and how fast it regenerates |

**Height** is a separate physical trait (5'9"–7'4"). It changes the hitbox, release point, reach and speed, so it's a real trade-off rather than a free buff.

**Balance rule:** every character gets the same attribute budget (for example 54 points). Taller characters pay "tax" for height. That makes all characters viable, and balance can be tuned in data files rather than code.

### 4.2 Archetypes

| Archetype | Shape | Feel |
|---|---|---|
| **Sharpshooter** | High Shooting, low Strength | Wide green window, deadly from 3, gets bullied in the post |
| **Slasher** | High Speed, Finishing and Vertical | Lives at the rim, highlight dunks |
| **Big** | Tall, high Strength and Defense | Walls off the paint, slow and a poor shooter |
| **Floor General** | High Handles and Agility | Ankle-breakers, the best passer in 2v2 |
| **Lockdown** | High Defense and Agility | Steals and contests, a weak scorer |
| **Two-Way Star** | Balanced, no weakness | The beginner-friendly pick |

### 4.3 Roster
- **Launch with 12 original characters**, 2 per archetype. They're **not** real NBA players: original big-head characters with their own personalities, home courts and voice lines. This avoids licensing problems and gives us IP we own.
- Each character has a **Signature Move** (their Super):
  - *Sharpshooter:* "Deep Range": for 8 seconds the green window covers the whole meter from anywhere past half court.
  - *Slasher:* "Takeoff": a sprint where any dunk from the free-throw line can't be blocked.
  - *Big:* "The Wall": a single ultra-wide block window, and a block in it returns possession.
  - *Floor General:* "Freeze": the next crossover is a guaranteed ankle-breaker.
  - *Lockdown:* "Pickpocket": the next steal attempt can't fail if it's in range.
  - *Two-Way:* "Clutch": every attribute gets +2 for 10 seconds.
- **Post-launch:** 2 new characters per season, plus a Create-a-Player mode where you spend the same attribute budget yourself (casual and private lobbies only, so ranked stays readable).

---

## 5. Modes

| Mode | Players | Notes |
|---|---|---|
| **Quick Play (online)** | 1v1, 2v2 | Unranked, fast matchmaking, with an AI fill-in if the queue is slow |
| **Ranked (online)** | 1v1, 2v2 | Seasonal ladder (Bronze through Legend) using Glicko-2, with a separate rating per queue |
| **Private Lobby** | 1v1, 2v2 | Share a 6-character code or link, with custom rules: score, clock and modifiers |
| **Local Couch** | 2–4 | The same machine, with keyboard plus gamepads. It keeps the original's best feature |
| **vs AI** | 1v1, 2v2 | 4 difficulties. AI follows the same rules and input limits (no cheating) |
| **Tour (campaign)** | Solo | A ladder across 8 courts with bosses and unlockable cosmetics |
| **Training** | Solo | An interactive tutorial plus a free-practice sandbox with a shot-meter overlay |
| **Party Modes** (rotating) | Any | 21, H-O-R-S-E, Dunk Contest, Low-Gravity, Giant Ball, 3-Point Shootout |

---

## 6. Progression and retention (no pay-to-win)

- **Account level (XP)** unlocks cosmetics and characters. All 12 launch characters can be unlocked within about 10 hours of play.
- **Cosmetics:** jerseys, shoes, balls, dunk effects, celebrations, court skins, player cards and banners.
- **Daily and weekly challenges** ("Win 2 games with a Big", "Hit 5 perfect releases") reward XP and coins.
- **Seasons** (about 8 weeks): a ranked reset, new characters, a free track plus an optional premium cosmetic track.
- **Profile stats:** FG%, 3P%, blocks, ankles broken, perfect releases and most-played characters.
- **Replays and clips:** the last 10 seconds can be exported as a GIF or MP4. This is cheap with a deterministic sim (inputs only) and gives us free viral marketing.

---

## 7. Technical architecture

### 7.1 Platform
**Web first** (HTML5, desktop + mobile), like the original, so the game opens instantly from a link and can go on CrazyGames, Poki or itch.io. Later it can be wrapped for Steam (Electron or Tauri) and mobile app stores (Capacitor).

### 7.2 Stack

| Layer | Choice | Why |
|---|---|---|
| Language | **TypeScript** everywhere | One language for the sim, client and server |
| Monorepo | pnpm workspaces + Turborepo | Shared sim package |
| **Simulation** | Custom, deterministic, fixed-step at 60 Hz in `packages/sim` | Required for rollback, replays and anti-cheat |
| Physics | Custom 2D (AABB/capsule players, circle ball, rim as 2 circles + a backboard segment) | General-purpose engines (Matter.js) aren't deterministic across machines, and our needs are simple |
| Rendering | **PixiJS v8** (WebGL / WebGPU) | Fast 2D, sprites and particles, works on mobile |
| Animation | Spine 2D or DragonBones skeletal rigs | Smooth, swappable cosmetics, small file size |
| Audio | Howler.js | Simple and mobile-safe |
| UI / menus | React + Tailwind (DOM overlay) | Faster to build menus than in canvas |
| Transport | WebSocket now, WebTransport (UDP-like) later | WebSocket works everywhere |
| Game server | Node.js + uWebSockets.js, one process hosting many rooms | Runs the same `sim` package as the client |
| Backend / DB | **Supabase** (Postgres, Auth, Edge Functions) | Accounts, profiles, inventory, ranked ratings, leaderboards |
| Web hosting | **Vercel** for the site, menus and static assets | Already connected |
| Game-server hosting | **Fly.io** (multi-region: US-E, US-W, EU, Asia) or Hetzner | Vercel can't host long-lived WebSocket servers |
| Analytics / errors | PostHog + Sentry | Funnels, retention and crash reports |

### 7.3 Netcode: the most important technical decision

**Model: deterministic lockstep with rollback (GGPO-style), relayed and validated by an authoritative server.**

1. The whole game state is a pure function: `step(state, inputs[]) -> state`. There's no `Math.random()` (we use a seeded PRNG in the state), no wall-clock time, and no iteration order that depends on a `Map`.
2. **Determinism across browsers and Node:** use **fixed-point math** (16.16 integers) or carefully limited float ops (`+ - * /` and `sqrt` only, with lookup tables for trig). We test it by hashing state every frame (§8).
3. Clients send **only inputs** (about 2 bytes per frame, batched with redundancy for the last 8 frames to cover packet loss).
4. Clients **predict** the remote player's input (repeat their last input). When the real input arrives and differs, they **roll back** to that frame and re-simulate, which takes under 1 ms per frame because the sim is tiny.
5. **Input delay:** 1–3 frames, chosen automatically from ping, to cut down visible rollbacks.
6. The **server** relays inputs and runs the same sim. It's the source of truth for the final score, ranked results and desync detection. If a client's state hash diverges, the server's state wins (resync snapshot), and that's logged.
7. **Anti-cheat for free:** clients can only send inputs, never positions or scores. Speedhacks are impossible because the server enforces the input cadence.
8. **Reconnect:** a dropped player gets a snapshot plus the input log and rejoins in under 3 seconds, while AI takes over their player during the gap.
9. **Target:** feels local at 0–80 ms RTT, playable at 150 ms. Match players by region and ping.

This is the same approach that makes fighting games and Rocket League-style physics feel great online, and it suits small player counts (2–4) well.

### 7.4 Repo layout
```
/apps
  /client        # PixiJS game + React menus (Vite)
  /server        # Node game server (rooms, relay, validation)
  /web           # Landing page, account pages (Next.js on Vercel)
/packages
  /sim           # Deterministic game logic + physics (no DOM, no Node APIs)
  /shared        # Net protocol, types, constants, character data (JSON)
  /ai            # Bot logic (runs on top of sim; used offline + as fill-ins)
/tools
  /balance       # Simulate thousands of bot-vs-bot games -> win-rate tables per character
  /netsim        # Latency/jitter/packet-loss simulator for testing
/assets          # Source art, Spine projects, audio
```

### 7.5 Data-driven balance
Characters, shot curves, dunk radii and the timing windows all live in `packages/shared/data/*.json`. Designers tune numbers without touching code. The balance tool runs headless bot-vs-bot matches overnight and flags any character with a win rate outside 47–53%.

---

## 8. Quality and testing

- **Determinism tests (CI):** replay recorded input logs in Node and in headless Chromium (Playwright), then compare state hashes for every frame. Any mismatch fails the build.
- **Unit tests** for physics: rim bounces, shot trajectories and collision edge cases.
- **Netcode soak test:** bots play 1,000 matches through `netsim` at 0, 50, 100 and 200 ms RTT with 0–5% loss and jitter. Check for zero desyncs and measure rollback frequency.
- **Load test:** 1 server instance should hold at least 200 concurrent rooms.
- **Playtesting:** weekly sessions with 5–10 outside players, recording "fun score", confusion points and rage moments.
- **Performance budget:** 60 FPS on a 2019 mid-range Android phone, under 8 MB initial download, and under 3 seconds to interactive.

---

## 9. Art and audio direction

- **Style:** clean, bold, flat-shaded cartoon with big heads (about a 1:2 head-to-body ratio), thick outlines and saturated team colors. It should be readable at small mobile sizes.
- **Courts:** 8 themed courts (rooftop at sunset, neon night court, beach, subway, arena, snowy park, retro 80s, space station for low gravity).
- **UI:** minimal. The HUD has the score, shot clock, super meter and stamina ring under the player, and nothing else. Menus are snappy, with 150 ms transitions.
- **Music:** original lo-fi and hip-hop instrumentals, with stems so the music intensifies in close games.
- **Accessibility:** colorblind-safe team palettes, remappable keys, a sound cue for shot timing, and a reduced-motion option to turn off screen shake.

---

## 10. Roadmap

Time estimates assume 1–2 developers plus part-time art and audio help. Each phase has a "done when" gate, and we don't move on until it's met.

### Phase 0: Find the fun (weeks 1–4)
- Set up the monorepo, `sim` package, fixed-step loop and PixiJS renderer with placeholder shapes.
- Build movement, jumping, ball physics (rim and backboard), the timed-release shot, the dunk, blocks and steals.
- Local 1v1 on one keyboard.
- **Done when:** 1v1 with gray boxes is fun for 10 minutes straight, judged by 5 people outside the team.

### Phase 1: Vertical slice (weeks 5–10)
- 4 characters (Sharpshooter, Slasher, Big, Two-Way) with the attribute system and Signature Moves.
- First skeletal art and animation pass, 1 polished court, the core juice (hit-stop, particles, camera, SFX).
- A first AI opponent, plus a training mode and tutorial.
- **Done when:** a stranger can learn the game in 60 seconds and wants to play again.

### Phase 2: Online multiplayer (weeks 11–18)
- Determinism hardening (fixed-point math, hash tests in CI).
- Rollback netcode, the game server, private lobbies with codes, and 1v1 online.
- `netsim` testing, reconnect, and a ping display.
- Supabase auth: guest play first, with an optional upgrade to Google or Discord login.
- **Done when:** a 100 ms RTT match feels close to local, with zero desyncs in a 1,000-match soak test.

### Phase 3: 2v2 and content (weeks 19–26)
- 2v2 (online and couch): passing, alley-oops and screens.
- Full 12-character roster, 4 courts, and Tour mode.
- Matchmaking (Quick Play), regional servers.
- Mobile touch controls and performance pass.
- **Done when:** closed beta with 100–300 players, D1 retention of 35% or more, and an average of 5+ matches per session.

### Phase 4: Launch (weeks 27–32)
- Ranked mode with Glicko-2 and seasons, plus progression, cosmetics and daily challenges.
- Replays and clip export.
- Party modes, the remaining courts, music, announcer, and the accessibility pass.
- Submit to CrazyGames and Poki, alongside our own site on Vercel.
- **Done when:** a public launch is stable at 1,000+ concurrent players with under a 0.5% crash rate.

### Phase 5: Live game (ongoing)
- 8-week seasons with 2 new characters, a new court and a new party mode each.
- Create-a-Player, 3v3, tournaments with brackets, clubs and crews, and spectator mode.
- Native wrappers for Steam and mobile app stores.

---

## 11. Risks and mitigations

| Risk | Mitigation |
|---|---|
| **Name and IP:** "Basketball Legends" is already MadPuffers' sequel title, and real NBA names need a license | Pick an original name before any public release, ship only original characters, and do a trademark search first |
| Desyncs ruin online play | Make determinism the sim's first rule from day 1, with hash tests in CI and server resync as a fallback |
| Online lobbies are empty at launch | Bot fill-in for Quick Play, strong couch and AI modes, private-lobby links that are easy to share, and launch on portals that already have players |
| Balance complaints | Data-driven tuning, automated bot simulations and seasonal balance patches |
| Mobile performance | Set the performance budget early, test on a low-end phone every sprint, and use sprite atlases plus object pooling |
| Scope creep | Each phase's "done when" gate is the only thing that matters. Ideas go in a backlog for Phase 5 |

---

## 12. Success metrics

- **Feel:** under 50 ms input-to-screen latency locally. Online rollbacks are invisible 95% of the time at 80 ms or less.
- **Fun:** average session of 5+ matches. Over 70% of new players finish the tutorial.
- **Retention:** D1 at 35% or more, D7 at 12% or more, D30 at 5% or more.
- **Fairness:** every character's win rate between 47% and 53% in ranked.
- **Rating** on portals (CrazyGames/Poki) above 8.5, beating Basketball Stars' 8.4.

---

## 13. Immediate next steps
1. Pick an original game name and the first 4 characters (names, archetypes, looks).
2. Set up the monorepo (`apps/client`, `packages/sim`) with Vite + PixiJS + Vitest.
3. Build the Phase 0 prototype: movement, ball physics and the timed shot, in local 1v1.
4. Run the first "is it fun?" playtest in week 4.
