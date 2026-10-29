# Masterplan v2: a competitive arcade basketball game

> Working title: **Basketball Legends** (rename before any public release, see §17).
> v1 aimed for "Basketball Stars, but better". v2 aims higher: **a basketball game with the depth of a fighting game and the teamwork of Rocket League.** It should be easy to pick up and take years to master, and it should be fair enough to run tournaments on.

---

## 0. What changed from v1

| v1 | v2 |
|---|---|
| Side-view 2D court with one depth lane | **True 3D court logic** (players move on a 2D floor plane and jump vertically) with a broadcast camera. This makes spacing, passing lanes and screens real |
| 1v1 / 2v2 | **Duel (1v1)** and **Squad (3v3)** as the competitive formats. 2v2 is casual |
| 4 action buttons | A **layered control scheme**: simple preset for new players, full **Pro controls** with a dribble stick, defensive hands and coverage calls |
| One super per character | A **team Momentum meter with 3 bars**: spend it on enhanced moves or save it for a signature |
| Shot meter + physics | A **zero-RNG Shot Quality model** where every miss can be explained, plus timing windows measured in frames |
| Steals by stat roll | A **visible dribble rhythm**. Steals work only when the ball is exposed. It's a pure read, with no dice |
| 9 attributes | **18 attributes**, a physical profile, a passive trait, a signature, and a **badge loadout** |
| Ranked ladder | Ranked with **draft and ban**, Bo3 duels, team queue, spectator mode, tournament lobbies, replay takeover and a training lab |

---

## 1. What we're competing with (and beyond)

**Basketball Stars** (MadPuffers, 2019, browser, rated 8.4 on CrazyGames) is a fun 2-minute toy. It has no online play, no 3-pointers, makes are close to random, and the characters are near-identical. It has **no competitive future**.

Our real benchmarks are the games that built lasting competitive scenes:
- **Fighting games** (Street Fighter, Smash): frame data, mixups, reads, meter management, training mode, Bo3 sets.
- **Rocket League**: a simple goal, a physics skill ceiling, 3v3 rotations, mechanics that took years to discover.
- **NBA 2K Park/Rec**: player builds, badges, competitive 3v3 streetball. Our edge over it: no pay-to-win, no RNG, instant browser play, and readable arcade pacing.

---

## 2. Competitive pillars

1. **Zero RNG.** Every outcome is decided by inputs, positions and stats. Two identical possessions always end the same way, so a loss is something to learn from rather than bad luck.
2. **Every option has a counter.** No move, character or badge is safe in every situation. Depth comes from reads, not from unlocking things.
3. **Readable at all speeds.** A spectator or a beginner can tell why a play worked. After each shot, the game shows why it went in or missed.
4. **The skill floor is low and the skill ceiling is very high.** Simple controls get you playing in 60 seconds. Pro controls add about 10× more options.
5. **The netcode is the product.** Rollback, server-validated, region-matched. If online play doesn't feel local, nothing else matters.
6. **Fair forever.** Cosmetics only. Every character and badge is unlockable by playing, and in ranked everything is unlocked.
7. **Fun to watch.** Highlight plays happen naturally. There's a spectator mode, stats and a replay system from day one.

---

## 3. Formats and rules

### 3.1 Competitive formats

| Format | Players | Structure | Length |
|---|---|---|---|
| **Duel** | 1v1 | **Best of 3 games**, each to **11** (1s inside the arc, 2s outside), win by 2 with a cap of 15. The loser of a game picks their character first for the next (fighting-game counter-pick) | ~3 min/game, ~10 min per set |
| **Squad** | 3v3 | One game to **21** or **8:00**, FIBA 3x3 style (1s and 2s). Overtime: the first team to score 2 wins | ~8–10 min |
| Doubles (casual) | 2v2 | To 15 | ~5 min |

### 3.2 Half-court rules (based on FIBA 3x3, adjusted for arcade play)
- **12-second shot clock.**
- **Check ball** at the top of the key after a make. On a defensive rebound or a steal, the ball must be **cleared** past the arc before you can score. Scoring without clearing is a violation.
- **Fouls:** a shooting foul inside the arc gives 1 free throw, and behind the arc gives 2. **Team bonus:** fouls 7–9 give 2 FTs, and from foul 10 on it's 2 FTs + possession. **Charge vs. block** depends on whether the defender was planted (§4.5).
- **Violations:** travel (picking up the dribble then moving your pivot foot), double dribble, goaltending, moving screen, 5-second closely guarded with a picked-up dribble.
- **No make-it-take-it.** Possession alternates on a make, which keeps it fair.

---

## 4. Core systems

All timings are in **frames at 60 Hz** (1f ≈ 16.7 ms). Every number lives in data files and is tunable (§13.5).

### 4.1 Movement and momentum
- Players have **velocity with acceleration and deceleration curves**, not instant direction changes. Speed and Acceleration stats shape the curves.
- **Plant and cut:** reversing direction at speed forces a plant (6–12f depending on Agility). Good players hide their plants behind dribble moves.
- **Balance** (0–100, hidden in casual and shown in Pro HUD/training): sharp cuts, contact and landing off-balance lower it. Balance affects shot quality, finishing and how easily you get bumped.
- **Pivot:** once you pick up the dribble, your pivot foot is locked automatically. Pivoting and pump fakes are your only moves left.
- **Hop step, gather, euro step:** used near the rim (§4.5).

### 4.2 Ball handling: the dribble rhythm
This is the core of the 1v1 duel.

- The dribble has a **visible rhythm** (about 24f at walk, 16f at sprint). The ball is **exposed** during some frames (in front, low) and **protected** during others (in the hand away from the defender, or behind the body).
- **Ball hand matters.** Dribbling in the hand nearest the defender exposes the ball longer. Switching hands takes a dribble move.
- **Dribble moves** (Pro: right stick, Simple: one "Move" button picks one based on context):

| Move | Pro input | Startup | Recovery | Handle Load | What it beats | What beats it |
|---|---|---|---|---|---|---|
| Hesitation | RS forward tap | 6f | 6f | 10 | A defender in Sprint (freezes them 10f) | A defender holding stance |
| Crossover | RS flick across | 8f | 10f | 15 | Shading the wrong side | Reach-steal timed on the exposed frames |
| Between legs | RS back-across | 10f | 8f | 20 | Steal attempts (ball protected) | Tight body-up (slow) |
| Behind back | RS back | 12f | 6f | 25 | Hands-low defender | Gives up ground vs. a well-positioned defender |
| Spin | RS half-circle | 14f | 12f | 30 | Shading, defenders who overplay | Planted defender, gives a **charge** |
| In-and-out | RS out-in | 7f | 8f | 15 | Defenders who bite on crossover reads | Patient defenders |
| Step-back | RS away + Shoot | 10f | n/a | 20 | Tight closeouts (creates ~1.5 ft) | Long-armed contests (Wingspan) |

- **Chains:** any move can be **cancelled into another** in its last 4 recovery frames. A chain is a combo, and each move has its own counters, so chains are mixups.
- **Handle Load:** every move adds load, and load drains at 30/s. If it goes above the cap (based on the Handles stat, 80–140), you get a **loose dribble**: a 20f window where the ball is fully exposed. This prevents infinite combos and makes rhythm and restraint a skill.
- **Ankle-breaker:** if a move's direction goes against the defender's committed momentum (Sprint, or mid-plant), the defender **stumbles**. The stumble lasts 20–50f, depending on the Handles vs. Agility difference. It's deterministic, never random.

### 4.3 On-ball defense
Defenders control three things at once. That makes defense a skill rather than standing still.

1. **Stance**
   - **Slide** (hold LT): lateral speed bonus, can react to dribble moves, slower straight-line speed.
   - **Sprint:** fast, but **any dribble move against you is a free ankle-breaker check**.
   - **Body-up:** used at contact range. Strength battle that slows the ball handler. Vulnerable to spin.
2. **Shading** (left stick while in Slide): offset your position to take away one hand or direction. The offense attacks the other side, and that is the read.
3. **Hands** (Pro: right stick; Simple: automatic)
   - **Hands Up:** big shot contest bonus. The ball handler gets a first-step bonus on drives.
   - **Hands Low:** a **reach-steal** is available, and good contest on drives. Weak contest on quick shots.
   - **Reach-steal** (B): succeeds **only if your hand hitbox touches the ball during an exposed dribble frame**. Missing costs 18f of recovery, and touching the body is a **reach-in foul**.
- **Closeout:** sprinting toward a shooter then braking (the Closeout input) gives a contest bonus, but a hard closeout is easy to blow by. Short closeouts are safe but contest less.

**The perimeter triangle** (this is the basis of every on-ball possession):

| Offense \ Defense | Sag off / Hands Low | Tight / Hands Up | Shaded |
|---|---|---|---|
| **Pull-up shot** | ✅ open shot | ❌ heavy contest | ➖ depends on hand |
| **Drive** | ❌ walled off | ✅ first-step bonus | ✅ if attacking the open side |
| **Dribble move** | ➖ no reaction to punish | ✅ ankle-breaker risk | ✅ if it switches sides |

### 4.4 Shooting: the zero-RNG Shot Quality model
Every shot has a **Shot Quality (SQ)** score from 0–100. The score turns into a **launch error vector** (angle and power), and the ball then flies with real physics. **There is no dice roll.**

```
SQ = ZoneRating(shooter, spot)      // Close/Mid/Three ratings, distance falloff
   × Timing(release frame)          // Perfect / Good / Early / Late
   × Openness(contest)              // defender distance, height, wingspan, hands, closeout quality
   × Balance(momentum, feet set)    // catch-and-shoot > pull-up > fadeaway > drifting
   × Fatigue(energy)
   + Badge / Trait modifiers
```

- **Release windows** (example: open 3-pointer, Three rating 9): **Perfect = 3f**, **Good = ±5f**. Lower ratings, contests and fatigue shrink both windows. **Perfect + SQ ≥ 90 always goes in.**
- **Misses can be explained:**
  - Early releases miss **short** and late releases miss **long**.
  - A shot taken while drifting misses **toward the direction of the drift**.
  - Heavy contests flatten the arc, so the ball **rims out**.
- **After the shot, the HUD shows a readout** (always in training, optional in matches), for example: `Release: Perfect · Contest: Heavy −38% · Balance: Drifting −10% → SQ 41 → short-left`.
- **Shot types** come from context and modifiers: catch-and-shoot, pull-up, step-back, side-step, fadeaway, floater, hook, bank shot (hold LB during the release; it has a separate window and is better from angles).
- **Pump fake:** tap Shoot and cancel within 8f. A defender who jumps is in the air for about 40f, which gives you a free blow-by or a foul-drawing shot.
- **Target shooting percentages** (we tune toward these): an open catch-and-shoot 3 from an elite shooter goes in about 60% of the time for top players, and a contested one about 25%. These percentages measure execution, not luck.

### 4.5 Finishing at the rim: the paint triangle
Driving into the paint and pressing Shoot starts a **gather** (2 steps). During the gather, the left stick chooses the finish:

| Finish | Input during gather | Beats | Loses to |
|---|---|---|---|
| **Power dunk/layup** | Toward the rim | Wall-up (Strength + Dunk vs. Strength), late blocks | **Charge** (defender planted in your path) |
| **Euro step** | Sideways | **Charge**, wall-up from the front | **Weak-side block** (help defender timing) |
| **Floater** | Early press (before the 2nd step) | **Blocks** (released before the big arrives) | **Wall-up / verticality** (low SQ when contested up close) |
| Reverse | Under the rim, away | Shot blockers on the strong side | A defender who stays home |
| Up-and-under | Pump, then shoot | A defender who jumps early | Patient defender |

Defensive answers:
- **Block** (jump): your hand hitbox has to touch the ball. If it touches the body, it's a foul, and a touch after the ball's peak is goaltending.
- **Take a charge** (planted outside the restricted arc for ≥20f before contact): offensive foul, and possession flips.
- **Wall-up** (jump straight up, hands up): never a foul. It cuts SQ heavily but can be dunked on with enough Strength.

### 4.6 Post game
- Hold LT with your back to the basket to **back down**. Each step is a **Strength + Post vs. Strength + Interior D** push.
- **Moves:** drop step, spin, hook shot, fadeaway, up-and-under, face-up. Each one has frame data, like the dribble moves.
- **Post defense:** body up, front (stops the entry pass but is lobbable), three-quarter.
- This gives bigs a real offensive identity and gives guards a reason to avoid switches.

### 4.7 Passing (Squad / Doubles)
- **Pass types:**
  - **Chest:** the fastest, but can be intercepted in the lane.
  - **Bounce:** slower, but hard for tall players to pick off.
  - **Lob:** goes over defenders and sets up **alley-oops**, but it's slow.
  - **Skip:** goes cross-court.
  - **No-look:** requires a badge, and the defender gets no tell.
- **Targeting:** aim with the left stick, or press the receiver's button (Pro: face buttons map to teammates). Passes **lead** a moving receiver automatically.
- **Interceptions follow physics:** a defender whose hand hitbox crosses the ball's path intercepts it. Hands Up and active stances enlarge the hitbox, and a deflection leaves a **loose ball**.
- **Pass fake:** tap and cancel. It baits interception attempts and help rotations.
- **Ball movement is rewarded:** a pass in the last 2 seconds before a shot gives a catch-and-shoot bonus to the shooter's Balance.

### 4.8 Off-ball play and team defense (Squad)
This is the team-strategy layer.
- **Screens:** off-ball players press Screen to set one. A screen set while moving is a **moving screen** violation. The ball handler uses it by running their defender into the screener's body.
- **Cuts:** backdoor cuts against overplaying defenders, flare and curl off screens. Off-ball sprinting costs energy, so cuts must be timed.
- **Defensive coverages** are set on the D-pad per defender, or by the team captain:
  - **Switch:** safe, but creates mismatches (a big on a guard).
  - **Fight over:** stays on your player, but can get stuck on the screen.
  - **Go under:** gives up an open pull-up.
  - **Hedge or trap:** sends 2 defenders to the ball and leaves someone open on the roll.
- **Help defense:** stepping into the drive lane gives a help-wall bonus, but leaves your own player open. **Rotation** is the 3v3 skill.
- **Pings and callouts:** a radial wheel with "Screen me", "Iso", "Switch!", "Help!", "Open!" and "Clear out", shown on court and played as voice lines. There's no need for voice chat.

### 4.9 Rebounding and loose balls
- **Box out:** hold LT near an opponent while a shot is in the air. It's a position and Strength battle.
- Rebounds follow the physics of where the ball comes off the rim. Players who **read the miss** (short, long, left or right, see §4.4) get there first. This makes the readable-miss system useful on defense too.
- **Tip-ins** and **putbacks** have their own release window. **Dive** (a sprint input while near a loose ball) gets there faster but leaves you on the floor for 30f.

### 4.10 Energy (stamina)
- Sprinting, dribble moves, jumps, contact and off-ball sprints all cost energy. Energy recovers while you walk, during dead balls and off the ball.
- **Low energy** shrinks release windows, slows acceleration and lowers the Handle Load cap.
- In Squad this creates natural **role rotation**: the player handling the ball tires, so they pass and recover off the ball.

### 4.11 Momentum meter (team resource)
- One **shared meter per team with 3 bars**. It fills from high-SQ makes, stops (a defensive possession with no score), blocks, steals, charges, assists and ankle-breakers. Getting scored on gives a small amount (anti-snowball).
- **Spending it:**
  - **1 bar, Burst:** the next action is enhanced. For example, a crossover with −3f startup, a contest with +25% contest strength, or an extra-fast first step. It's the equivalent of a fighting game's EX move.
  - **3 bars, Signature:** the character's signature move (§5.3).
- **Meter management** decides games: spend early for stops, or bank it for the last possession.

---

## 5. Characters and builds

### 5.1 Attributes (18, rated 1–20)

| Physical | Offense | Defense |
|---|---|---|
| Speed | Close Shot | Perimeter D |
| Acceleration | Mid-Range | Interior D |
| Vertical | Three-Point | Steal |
| Strength | Free Throw | Block |
| Stamina | Dunk | Rebound |
| Agility | Handles | |
| | Passing | |
| | Post Control | |

**Physical profile** (fixed per character, not budgeted, but it affects everything):
- **Height** (5'8"–7'5"): hitbox, release point, reach and rebound radius.
- **Wingspan** (−2" to +8" vs. height): contest radius, steal reach and pass-lane hitbox.
- **Weight**: Strength battles and momentum. Heavier players are harder to stop and also slower to change direction.

**Budget rule:** every character has the same attribute budget. Height and wingspan cost budget points, which is what makes the trade-offs real.

### 5.2 Archetypes
- **Sharpshooter** (off-ball scorer, pull-up threat)
- **Shot Creator** (handles + mid-range, iso duelist)
- **Slasher** (speed, dunks, finishes in traffic)
- **Floor General** (passing, handles, makes the team better)
- **Lockdown** (perimeter D, steal, wingspan)
- **Stretch Big** (height + three, pulls the rim protector out)
- **Rim Protector** (block, rebound, interior D)
- **Post Scorer** (strength, post moves, draws fouls)
- **Two-Way** (balanced, the beginner-friendly pick)

### 5.3 Character kit
Each character has:
1. **Attributes + physical profile**
2. **A passive trait** that is always on and defines their identity. For example:
   - *"Quick Trigger"*: catch-and-shoot startup −2f.
   - *"Iron Base"*: can't be bumped while planted.
   - *"Shifty"*: chain cancels open 2f earlier.
   - *"Eyes in the Back"*: no-look passes without the badge.
3. **A signature move (3 bars).** Strong, with a clear counter:
   - *Sharpshooter:* "Limitless": for 6 seconds, the release window doesn't shrink with distance. Counter: deny the catch.
   - *Shot Creator:* "Isolation": teammates clear out and the defender can't receive help for 5 seconds. Counter: the on-ball defender spends Burst.
   - *Slasher:* "Takeoff": dunk radius ×1.5 for 1 possession. Counter: take a charge.
   - *Rim Protector:* "No Fly Zone": the block window is doubled in the paint for 8 seconds. Counter: shoot floaters and kick out for 3s.
   - *Lockdown:* "Shadow": the ball handler's Handle Load cap is −40% for 6 seconds. Counter: pass.
   - *Floor General:* "Orchestrate": every teammate gets a +SQ catch-and-shoot bonus for 1 possession. Counter: deny passes.

**Launch roster:** 18 original characters, 2 per archetype. They're original big-head stylized characters with personalities, home courts and rivalries. **No real NBA players.**

### 5.4 Badges (build loadout)
- A pool of about 45 badges. Each costs **1–3 points**, and every build has **8 points**.
- Each archetype can use certain badge categories, which stops builds from collapsing into one "meta".
- **Badges change numbers by small, readable amounts**, such as frame data, windows or hitboxes. They never give an automatic win.
- Examples:

| Badge | Cost | Effect |
|---|---|---|
| Quick Release | 2 | Jump-shot startup −2f |
| Ankle Breaker | 2 | Stumble duration +10f |
| Unpluckable | 2 | Exposed dribble frames −2 |
| Clamps | 3 | Shading reacts to the first dribble move 3f faster |
| Interceptor | 2 | Pass-lane hitbox +15% |
| Glass Cleaner | 1 | Box-out push +10% |
| Posterizer | 3 | Dunk vs. wall-up: +2 Strength |
| Dimer | 2 | Shooter gets +5 SQ on your passes |
| Ice Veins | 1 | No fatigue penalty on free throws |
| Hustle | 1 | Dive recovery −10f |
| Charge Taker | 2 | Planted requirement 20f → 15f |
| Deep Range | 2 | Distance falloff starts 2 ft deeper |

- In ranked, players bring **3 saved loadouts per character** and pick one during the draft.

---

## 6. Draft and pick phase (ranked)

**Squad (3v3):**
1. **Ban phase:** each team bans 1 character (blind, simultaneous).
2. **Pick phase:** A1 → B1 B2 → A2 A3 → B3, snake style. No duplicates on a team, but the same character can appear on both teams.
3. **Loadout phase** (20s): choose a badge loadout. The enemy sees your characters but **not** your badges.

**Duel (1v1, Bo3):** blind pick for game 1. **The loser of each game picks first**, then the winner can counter-pick. Each player can ban 1 character per set.

---

## 7. Controls

### 7.1 Pro preset (gamepad first)

| Input | Offense with the ball | Offense off the ball | Defense |
|---|---|---|---|
| LS | Move / gather direction | Move | Move / shade |
| RS | **Dribble moves** / post moves | n/a | **Hands** (up / low) |
| RT | Sprint | Sprint | Sprint |
| LT | Post-up / protect ball | Screen / box out | **Slide stance** / box out |
| X / □ | Shoot (hold → release) | Call for ball | Contest / closeout |
| A / ✕ | Jump-stop / pump (tap) | Jump | Jump / block / rebound |
| B / ○ | Pass fake / spin (Simple preset) | Cut | **Reach-steal** / take charge (hold) |
| Y / △ | Lob pass | n/a | Switch call |
| RB | Pass (stick-aimed) | n/a | Intercept stance |
| LB | Modifier (bank shot, no-look) | n/a | **Burst** (spend 1 bar) |
| LB+RB | **Signature** (3 bars) | | **Signature** |
| D-pad | Plays / pings | Pings | **Coverage** (switch / over / under / hedge) |

### 7.2 Simple preset
- 4 buttons plus movement. One **Move** button picks a dribble move from context, stance is automatic, and passes are aimed for you.
- Simple players are in the same matchmaking, but an **identical hidden skill rating** means beginners won't face experts. Simple is a stepping stone toward Pro, not a crutch.

### 7.3 Keyboard and touch
- **Keyboard:** WASD to move, Shift to sprint, J/K/L/I for shoot/jump/steal/lob, **arrow keys as the dribble stick**, Space for slide stance, Q/E for passes, 1–4 for pings and coverages.
- **Touch:** virtual stick plus swipe gestures for dribble moves and passes. Touch is casual-only. Ranked requires gamepad or keyboard, because touch latency and precision can't compete fairly.

---

## 8. Example possession (Squad)

1. **Check ball.** Our Floor General brings the ball up. Our Rim Protector calls **"Screen me"**.
2. The opponents' Lockdown **shades** our handler to the left. Our handler hits a **hesitation**, but the defender holds stance and the hesitation does nothing. That's a good read by the defender.
3. Our Rim Protector sets a **screen**. The opposing team has **Switch** set, so their big now guards our Floor General. That's a **mismatch**.
4. Our Floor General goes into an **in-and-out → crossover chain**. The big is in Sprint stance, so it triggers an **ankle-breaker** and he stumbles for 38f.
5. Their help defender **rotates** into the lane, which leaves our Sharpshooter in the corner open.
6. **Skip pass**, a **catch-and-shoot**, and a **Perfect** release. SQ comes out at 94, so it's a guaranteed make for **+2**.
7. Our team earns **+1 Momentum bar** for the high-SQ assisted make.

Every step was a read and a counter-read. Every outcome is visible and explainable.

---

## 9. Training Lab (the fighting-game standard, and a big competitive draw)

- A **record and playback dummy**: record up to 5 sequences of inputs, and the dummy plays them back randomly. Use it to practice reacting to specific moves.
- **Frame data overlay:** startup, active and recovery frames for every move; dribble rhythm with exposed and protected frames highlighted; hitboxes (hands, body, ball).
- **Input display**, with frame-by-frame advance.
- **Shot Quality overlay:** a live SQ heatmap on the floor plus a breakdown after every shot.
- **Scenario editor:** place all 6 players anywhere, set the shot clock and meter, then save and share scenarios with a code.
- **Replay takeover:** pause any replay, **take control of any player at that frame**, and play the situation out. This is cheap because the sim is deterministic.
- **Drills with medals:** release timing, chains, closeouts, rotations. They double as the tutorial.

---

## 10. Competitive infrastructure

### 10.1 Ranked
- **Separate queues:** Duel, Squad Solo/Duo, and **Squad Team** (a 3-player premade with a team name, a team rating and a team page).
- **Rating:** Glicko-2 hidden MMR, with visible tiers **Rookie → Starter → Pro → All-Star → MVP → Legend**, plus a **Top 500** leaderboard per queue and per region.
- **Placement:** 5 matches. Seasons last 10 weeks with a soft reset. Inactivity decay only at MVP and above.
- **Squad Solo/Duo:** the MMR spread inside a party is capped, and duos are matched against duos where possible.

### 10.2 Integrity
- **Input-only clients:** the server verifies every match by running the deterministic sim (§13.3). Hacking positions or scores is impossible.
- **Macro and auto-release detection.** Automated timing scripts are the main cheat in a timing-based game. Detection flags:
  - Statistical outliers, such as near-zero variance in release frames across hundreds of shots.
  - Inhumanly regular input intervals.
  - Reaction times faster than humanly possible.

  Flagged accounts are reviewed through replays.
- **Leavers:** a bot (AI at the leaver's rating) takes over. Teammates don't lose rating if the leaver doesn't come back, and leavers get escalating queue bans.
- **Surrender:** a unanimous vote after 4:00 in Squad.
- **Smurf detection:** fast-track MMR for new accounts that perform far above their bracket. Ranked requires account level 10.

### 10.3 Spectator and esports
- **Spectator mode:** free camera, player POV, a broadcast camera, and live overlays (SQ, meter, energy, shot chart). There are up to 8 spectator slots per lobby, with an optional **broadcast delay**.
- **Tournament lobbies:** custom formats (Bo1/3/5), draft and ban, side selection, pause, and a rematch with counter-pick.
- **In-game weekly cups** (automatic brackets with cosmetic rewards). Later: an integration with start.gg for community tournaments.
- **Advanced stats on every player profile:**
  - Points, eFG%, TS%
  - **Average SQ** and **points over expected** (shot-making skill)
  - Assists, turnovers, stops, defensive SQ allowed
  - Ankle-breakers given and suffered
  - Clutch rating
  - Shot-chart heatmaps

### 10.4 Replays
- **Every match is saved automatically as input logs** (about 50 KB per match). It can be watched at any speed, from any camera, with replay takeover (§9).
- **Clips:** export any moment as an MP4 or GIF with one button.

---

## 11. Balance philosophy and live operations

- **What we balance for:**
  - Every archetype is viable at every rank.
  - No character has a pick+ban rate above 40% at the top tier.
  - Every character is picked at least 3% of the time.
  - Overall win rates stay between 47% and 53%.
- **Data sources:**
  - Pick, ban and win rates per rank tier.
  - **Bot-vs-bot simulations** of thousands of matches per balance change.
  - A pro player council.
  - Shot Quality distributions.
- **Cadence:** a patch every 2 weeks early on, then mid-season and start-of-season patches.
- **Hot balance:** balance data is versioned and **pinned per match**. The server sends the version hash at match start to keep determinism. Data changes need no client update.
- **Change notes always include exact numbers** (for example, "Spin startup 14f → 15f"). Competitive players want frame data.

---

## 12. Onboarding: how to keep the depth from scaring people off

1. **First 60 seconds:** Simple controls, against a bot, in a playable tutorial. A first dunk within 20 seconds.
2. **Tutorial chapters** teach one concept each: shooting windows, the perimeter triangle, the paint triangle, the dribble rhythm, screens. Each chapter ends with a "beat this bot" test that requires that concept.
3. **Bots teach counters:** for example, the "Gambler" bot reaches for steals constantly, so you have to learn to protect the ball.
4. **Casual playlists** (Quick Play, Party Modes) keep the arcade fun: H-O-R-S-E, Dunk Contest, Low-Gravity, Giant Ball, and 21 (every player for themselves).
5. **A "Pro controls" prompt** appears after 10 wins, with a 5-minute tutorial.

---

## 13. Technical architecture

### 13.1 Platform
- **Web first** (instant play from a link, available on game portals). The **same build is wrapped for Steam** (Tauri/Electron) for the competitive PC audience.
- Gamepad support from day one.
- Consoles possibly later. The sim is in Rust, so it can be ported.

### 13.2 Stack

| Layer | Choice | Why |
|---|---|---|
| **Simulation** | **Rust → WebAssembly** (`crates/sim`), fixed-point math, fixed 60 Hz step | Deterministic on every machine, fast enough for 6-player rollback (target **< 0.25 ms per step**), and the same binary runs in the browser and on the server |
| Client | TypeScript + Vite | Fast iteration |
| Rendering | **Three.js** (WebGPU, with a WebGL2 fallback), stylized toon-shaded low-poly characters, broadcast camera | 3D spacing is readable and it runs well on mid-range hardware |
| Animation | Skeletal clips **driven by the sim's state and frame data**. Animations never drive gameplay (no root motion) | Visuals can't desync, and frame data stays exact |
| UI | React + Tailwind overlay | Menus, draft screen, profiles |
| Game server | Rust (tokio) relay + verifier running the same `sim` crate natively | Thousands of rooms per machine |
| Transport | WebSocket now; **WebTransport/WebRTC data channels** (unreliable, UDP-like) as soon as possible | Lower latency jitter under packet loss |
| Backend | **Supabase** (Postgres, Auth, Edge Functions): accounts, MMR, loadouts, inventory, match history, leaderboards | Already connected |
| Web / site | **Vercel** (landing page, profiles, leaderboards) | Already connected |
| Game-server hosting | Fly.io or Hetzner, multi-region (US-East, US-West, EU, Asia, South America) | Low ping everywhere |
| Matchmaking | A Rust service using a Redis queue: matches on MMR, region ping and party size | |
| Observability | Sentry, PostHog, Grafana (server tick time, rollback rate, desyncs) | |

### 13.3 Netcode
- **Model:** deterministic **rollback** (GGPO-style) for all formats. The server relays and verifies.
  - Clients send only inputs, about 3 bytes per frame, with the last 10 frames repeated in every packet to cover packet loss.
  - Each client predicts remote inputs by repeating the last one, then rolls back and re-simulates when the real inputs arrive.
- **Making 6-player rollback work:**
  - Adaptive **input delay** of 2–3 frames, picked from the worst ping in the match.
  - A maximum rollback window of 8 frames. Beyond it, the client briefly stalls instead of mispredicting further.
  - A **very cheap sim step**. Six players re-simulating 8 frames is under 2 ms.
  - **Visual smoothing:** corrections to remote players blend over 4–6 frames, while gameplay state snaps immediately.
  - **Prediction-friendly design:** most decisive actions have ≥6f startup, which hides 100 ms of rollback.
- **Authority:** the server runs the sim and compares state hashes with the clients every 30 frames. If a client desyncs, it gets a resync snapshot and the incident is logged. **Server results are final** for ranked.
- **Reconnect:** a snapshot plus the input log lets a player rejoin in under 3 seconds. A bot covers the gap.
- **Targets:**
  - 0–80 ms RTT: feels local.
  - Up to 120 ms: competitively fair.
  - Above 150 ms: not matched in ranked.

### 13.4 Repo layout
```
/crates
  /sim          # Rust: deterministic game logic, physics, fixed-point math (no I/O)
  /server       # Rust: rooms, relay, verification, matchmaking client
  /bots         # Rust: AI built on the sim (training dummy, fill-ins, balance sims)
/apps
  /client       # TS + Three.js + React (loads sim.wasm)
  /web          # Next.js site on Vercel (profiles, leaderboards, news)
/packages
  /protocol     # Shared net message schemas (generated for Rust + TS)
  /data         # Characters, badges, moves, frame data, tuning (versioned JSON)
/tools
  /balance      # Headless bot-vs-bot simulation → win-rate & pick reports
  /netsim       # Latency/jitter/loss injector
  /replay-cli   # Verify, re-sim, and render replays
/assets         # Source art, rigs, audio
```

### 13.5 Data-driven everything
Moves, frame data, release windows, contest curves, badges and character stats all live in versioned JSON in `/packages/data`. A **balance editor** in the dev build lets you change a value and see it immediately in the Training Lab.

---

## 14. Quality and testing

- **Determinism CI:** a corpus of over 1,000 recorded matches is re-simulated on Linux (native), in WASM inside Chromium and Firefox (Playwright), and on ARM. **Every frame's hash must match.**
- **Property tests** for the physics: rim, backboard, collision and pass interception.
- **Netsim soak test:** bots play 10,000 matches at 0–200 ms RTT with 0–5% loss and jitter. Pass criteria: 0 desyncs, rollback depth p95 ≤ 4 frames at 80 ms.
- **Skill-expression test:** the expert bot must beat the median bot in **≥ 95% of Duel sets**. If not, there's too much randomness or the game isn't rewarding skill.
- **Performance:**
  - 60 FPS on 2020-era integrated graphics and mid-range phones (casual).
  - 144 Hz render with a 60 Hz sim and interpolation on PC.
  - Under 15 MB initial download.
- **Weekly playtests:** separate competitive-player and casual-player sessions.

---

## 15. Art, audio and accessibility (carried over from v1 and expanded)

- **Style:** bold, clean toon shading with big heads, thick outlines and team-color rim lights. **Silhouettes are distinct by archetype**, so you can read a matchup in a split second.
- **Readability:**
  - Every move with frame data has a distinct anticipation pose. Animation is a **tell**, which competitive players need.
  - The dribble rhythm's exposed frames show as a subtle glint on the ball.
- **Juice:** hit-stop on blocks and ankle-breakers, slow-mo on game-winners, crowd that reacts to score margin, announcer lines, and dynamic music stems.
- **Courts:** 10 themed courts at launch. They're all **gameplay-identical** in ranked, with no gimmicks.
- **Accessibility:**
  - Colorblind presets.
  - An audio cue for release timing, plus haptic pulses on gamepads.
  - Full remapping.
  - Reduced motion.
  - An adjustable HUD scale.

---

## 16. Roadmap (small team: 2–3 developers, 1 artist, part-time audio)

Each phase ends with a gate. **We don't move on until the gate passes.**

| Phase | Weeks | Scope | Gate |
|---|---|---|---|
| **0. Duel core prototype** | 1–6 | Rust sim plus graybox 3D: movement and momentum, dribble rhythm and 4 moves, stance/shading/hands, Shot Quality model, paint triangle, local 1v1 | 1v1 graybox is fun for 30+ minutes, **and** an experienced player beats a new one 9 times out of 10 |
| **1. Duel vertical slice** | 7–14 | 4 characters with full kits, fouls and free throws, Momentum meter, Training Lab v1 (frame data, dummy record), bots, first art and animation pass, 1 court | Outside testers can explain *why* they lost |
| **2. Online Duel** | 15–22 | Rollback + Rust server, lobbies, Bo3 sets, replays, Supabase accounts, ranked alpha (invite-only), netsim CI | 0 desyncs in 10k soak matches. 100 ms feels fair in blind tests |
| **3. Squad 3v3** | 23–32 | Passing, screens, coverages, help defense, rebounding, team meter, pings, draft and ban, 6-player rollback tuning | Closed-beta 3v3 sessions last 3+ matches on average |
| **4. Competitive layer and content** | 33–42 | 18 characters, badges and loadouts, ranked seasons and team queue, spectator mode, tournament lobbies, macro detection, advanced stats, Training Lab v2 (scenarios, replay takeover) | Closed beta of 1–2k players. All character pick rates between 3% and 15% |
| **5. Launch** | 43–50 | Onboarding chapters, party modes, cosmetics and progression, 10 courts, audio and announcer, accessibility, Steam build, portal release | Stable at 5k+ concurrent players, crash rate under 0.5% |
| **6. Live and esports** | ongoing | 10-week seasons (2 characters, 1 court, badge rotation), weekly cups, a seasonal championship, community tournament tools, 2v2 ranked | Growing number of active ranked players season after season |

**Key strategic choice:** launch ranked with **Duel first** (it needs only 2 players per match, so queues stay healthy). Squad ranked goes live once the player count supports it, with Squad casual available earlier.

---

## 17. Risks and mitigations

| Risk | Mitigation |
|---|---|
| **IP and name:** "Basketball Legends" is MadPuffers' own title, and real NBA players need a license | Original name, characters and branding. Do a trademark search before the beta |
| Too complex for casual players | Simple preset, onboarding chapters, casual playlists, and skill-based matchmaking that keeps beginners together |
| 6-player rollback has too many visible corrections | ≥6f startup design rule, adaptive input delay, visual smoothing. Fallback: server-authoritative prediction for 3v3 only |
| Empty ranked queues | Duel ranked first, regional merge at low population, bot fill for casual modes, portal distribution for player acquisition |
| Timing-script cheats | Statistical detection, replay review, server-side input cadence checks |
| One overpowered build dominates | Badge category limits, data-driven hot balance, bot sims before every patch |
| Animation workload (3D + frame-data readability) | Shared skeleton across all characters; differences come from proportions, not unique rigs. Build the move library once |
| Scope creep | Phase gates are law. New ideas go to the Phase 6 backlog |

---

## 18. Success metrics

- **Skill expression:** a 400-point MMR gap means the higher player wins at least 90% of the time. The expert bot beats the median bot in ≥ 95% of sets.
- **Feel:** under 50 ms input-to-photon latency locally. At ≤ 80 ms RTT, 95% of rollbacks are invisible.
- **Depth:** the share of Pro-control users keeps growing. Players are still discovering new tech months after launch, which is a good sign.
- **Retention:** D1 ≥ 40%, D7 ≥ 15%, D30 ≥ 7%. At least 30% of D30 players play ranked.
- **Diversity:** every character has a pick rate between 3% and 15% at the top tier.
- **Community:** weekly cups fill up. Clips and replays get shared organically.

---

## 19. Immediate next steps
1. **Pick the name** and design the first 4 characters (Shot Creator, Slasher, Rim Protector, Lockdown). Together they cover every Duel interaction.
2. **Set up the repo:** a Cargo workspace (`crates/sim`), the `apps/client` Vite + Three.js app, the wasm-bindgen pipeline, and determinism CI from day one.
3. **Build Phase 0** in this order: movement → dribble rhythm and steals → stance and shading → Shot Quality model → paint triangle.
4. **Week 6:** the gate playtest for fun and skill gap.
