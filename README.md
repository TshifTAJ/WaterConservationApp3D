# Water Conservation Challenge — 3D Desktop Build

A full 3D desktop implementation of the Water Conservation Educational Tool
spec (FR-01 to FR-09), built with Three.js and packaged as an Electron
desktop app. No game engine install required — just Node.js.

## What's new in this version — Reward "Juice" Layer, Quest Markers & Rig Polish

A focused pass on *feel* rather than new mechanics: every existing system
(collection, routing, filtering, quizzes, badges) now gives sharper,
game-like feedback in the moment, on top of the reward logic that was
already there.

- **New `js/effects.js` module** — particle bursts (blue water-droplet
  splash, gold sparkle, multicolour confetti), floating "+N" reward popups
  projected from world space onto the screen, and a full-screen flash. Built
  from `THREE.Points` and small DOM/canvas sprites only — no textures, no
  external assets, fully offline, same "no engine, no build step" philosophy
  as the rest of the project.
- **Combo/streak reward system**: clean collections at Water Warden,
  correct quiz answers, and other "good actions" now chain into a streak
  multiplier (1x → 1.5x at 3 in a row → 2x at 6 → up to 3x at 12+) if kept up
  within a 6-second window. Streaks pay out small bonus points with an
  escalating popup + chime every 3rd hit, layered on top of (not replacing)
  the existing +100-per-challenge and badge points.
- **NPC quest markers**: a bobbing gold "!" now floats above any NPC with an
  unclaimed challenge or stage on offer, so the next objective is legible
  from a distance instead of only discoverable by walking up to each NPC.
  NPCs also wave (reusing the existing procedural gesture) the first time
  you enter their radius while a quest is pending.
- **Reward moments now have visual+audio payoff**: badge awards and
  challenge completions trigger a confetti burst and screen flash at the
  player's position; bucket placement, clean-water pickup, correct
  pipe-routing, and starting the filter each get a matching splash/sparkle
  particle burst instead of just a sound and a HUD number change.
- **Idle rig polish**: characters now do a slow idle head-turn ("looking
  around") in addition to the existing breathing sway, so NPCs read as
  alive rather than frozen when you're not interacting with them.

**Honest scope note on this pass**: this was a feedback/"juice" layer on
top of the existing systems, not a structural rebuild. It does **not**
include imported skeletal rigs (see "Known simplifications" below — the
characters are still procedurally-built capsule/sphere rigs, not glTF
models with bone hierarchies) or autonomous NPC *agents* (the villagers
still wander on a simple scripted loop, and quest-giving NPCs are still
state-machine driven, not goal-directed). Both would be substantial
follow-on projects rather than something to bolt on safely in one pass —
see "Extending it" at the bottom for the natural next steps and why.

## What's new in the previous version — World & Cartoon Overhaul

- **Fixed camera bug**: adjusting the camera-angle slider in Settings now updates the view live, even while the menu is open (previously the whole game — including the camera — froze while Settings was open).
- **Easier navigation**: default camera now leans top-down out of the box (matches the fixed-direction WASD controls much more intuitively), tracks you more tightly, and a bright ground-facing arrow always shows which way you're heading regardless of camera angle.
- **Persistent objective banners**: each challenge now shows an always-visible "OBJECTIVE" banner (not just a dialogue line that vanishes after a few seconds) — Filter Fundi's updates step-by-step as you progress through collecting materials, building layers, and fetching water.
- **Cartoon character overhaul**: cel-shaded (toon) materials across the entire world, chibi-style proportions (oversized heads, rounded capsule limbs), simple expressive eyes, and each NPC now wears a distinguishing hat (cap / tool-belt / wide-brim) so they're instantly recognisable.
- **Richer environment**: rocks, bushes, a small vegetable garden patch (tying visually into the water-conservation theme), and gently looping ambient birds overhead.
- **3-tier environment detail**: Settings → Environment Detail now offers Minimal / Standard / Lush instead of a binary toggle, for finer control on lower-spec machines.
- **Educational signposts**: two interactable signposts placed in the open world let players read real, cited water-saving facts (reinforcing the same WRC/Springer Nature statistics used in the quizzes) just by exploring — not gated behind challenge completion.
- **Cumulative impact tracking**: total litres collected/saved/purified across all three challenges is tracked in the profile, and completing all three ("Water Guardian") now shows a summary translating your cumulative total into real-world equivalents (approximate toilet flushes / shower minutes).

## What's new in this version — Stage System, Real Techniques & Final Evaluation

**Bug fix**: challenges previously got stuck after Stage 1 — completing a
challenge reset the "talk to NPC" flag but never reset the challenge's own
internal state, so the NPC silently refused to offer Stage 2 or 3. Fixed:
completing any stage now properly returns the challenge to an idle state
so re-approaching the NPC correctly offers the next stage.

**Player name is now used throughout**: captured once at first launch,
NPCs address you by name at the start of every stage's instructions
("Tshifhiwa, Stage 2 — ..."), and it appears on the certificate.

This is the biggest update yet: every challenge now has **3 stages** of
increasing real-world depth, and completing all three at Stage 3 triggers
a genuine performance evaluation.

**Rainwater Collection (Water Warden)**
- Stage 1: core collection loop (as before)
- Stage 2: **first-flush diversion** — the first several seconds of runoff
  at each puddle is dirty (visibly brown) and doesn't count if collected;
  a real household technique for keeping debris out of a tank
- Stage 3: + **overflow management** — once the tank is full, you must
  open an overflow valve toward a soak-away bed before the challenge
  completes, mirroring how real systems prevent flooding

**Greywater Reuse (Pipe-Fitter Pete)**
- Stage 1: core pipe-routing puzzle (as before)
- Stage 2: **filter/lint trap requirement** — press **F** on a placed pipe
  to add a trap; your route won't validate without at least one, matching
  how real greywater systems filter debris before reaching a garden
- Stage 3: **diversion valve decision** — each attempt randomly flags the
  water as safe or contaminated (e.g. containing bleach); route it to the
  Garden or the Sewer accordingly — routing to the wrong one is corrected
  with an explanation, not just a fail state

**Filtration System Build (Filter Fundi)**
- Stage 1: core build-and-filter loop (as before)
- Stage 2: + **mandatory disinfection step** — even after turbidity drops
  to the WHO guideline, you must add a disinfection step before the
  challenge completes, since filtration alone doesn't guarantee
  pathogen-free water
- Stage 3: + **maintenance event** — partway through filtering, the top
  layer clogs (a real slow-sand-filter phenomenon) and needs a gentle rake
  to resume, rather than full teardown

**Supporting systems**
- **Difficulty setting** (Relaxed / Standard / Challenge) scales time
  limits, targets, and pipe budgets — makes the game genuinely playable
  for young children through to adults wanting a real test
- **Text size setting** (Small / Medium / Large) scales the entire UI
- **Hint system**: if you're stuck for ~20 seconds during an active
  challenge, a contextual hint appears automatically (max 2 per attempt,
  so it never becomes spammy)
- **Water Facts Journal** (📖 button or **J** key): every real-world fact
  encountered — from signposts, quiz explanations, and stage-unlocked
  techniques — is permanently logged here for review
- **Two educational signposts** in the open world reinforcing cited stats
- **"Try this at home" tips** on every challenge completion, translating
  in-game learning into an actual real-world action
- **Optional player name** at first launch, used in welcome-back messages
  and the certificate
- **Final Evaluation**: once all three challenges reach Stage 3, a
  performance report appears — total points, badges, quiz accuracy
  percentage, cumulative water impact, time played, and facts discovered,
  plus a qualitative "knowledge band" (Water Champion / Water Wise /
  Getting There / Needs Review) with constructive, specific critique
  based on actual quiz performance (not just challenge completion)
- **Certificate export as a real PDF**: from the evaluation screen (or the
  persistent 🎓 button once earned), save a properly designed, decorative
  certificate — player name in a script-style signature line, achievement
  band, stats grid, earned badges, techniques mastered, and a
  critique-based evaluation — anywhere on disk via a real OS save dialog.
  Generated entirely offline using Electron's built-in PDF engine, no
  external libraries.

## What's new in the previous version

- **Lively rural environment**: procedurally scattered trees, flower
  patches, and a small rural village of low-poly houses, plus decorative
  villagers who wander slowly for atmosphere.
- **Better navigation**: raised default camera angle, a live minimap
  (bottom-right) showing your position and all three challenge zones, and a
  compass hint pointing toward your nearest unfinished challenge.
- **Settings menu** (gear icon, top-right, or press **Esc**): movement
  speed, camera/eye angle, sound volume, background music toggle, spoken
  instructions toggle, environment detail, and language.
- **7-language UI**: English, Afrikaans, isiZulu, Sesotho, Setswana,
  isiXhosa, Tshivenda. **Important**: the five African-language translations
  beyond Afrikaans are AI-assisted drafts of short menu/UI text only — they
  have **not** been reviewed by a home-language speaker. The settings menu
  says this explicitly. Please get them checked before using this for
  formal submission or with real learners. Deeper dialogue and quiz content
  is English-only for now.
- **Procedural sound** (no external audio files, fully offline, no
  licensing risk): footsteps, water splashes, pipe-placement clicks,
  pickup/success/fail/badge chimes, ambient rain during the rainwater
  challenge, and a soft ambient background pad.
- **Spoken instructions**: each NPC's dialogue is read aloud using the
  browser's built-in offline text-to-speech (Web Speech API), with a
  distinct pitch/rate per NPC.
- **Online/offline indicator** (top-right): a real connectivity probe.
- **Filter Fundi's challenge is genuinely functional**: fetch real
  unfiltered water from a muddy river, build the filter in order, then
  watch murky water visibly drain through while a turbidity reading counts
  down against the real WHO guideline (<5 NTU).
- **Greywater challenge has a real resource constraint**: a limited pipe
  budget forces efficient routing (not unlimited free placement), and water
  visibly flows along your solved route once connected.
- **Expanded, randomized quizzes**: 5 researched questions per challenge,
  3 chosen at random each playthrough.
- **Milestone feedback and urgency cues** in the rainwater challenge:
  real-world equivalence messages as you collect litres, and a pulsing
  red vignette in the final 10 seconds.

## Requirements

- [Node.js](https://nodejs.org) (LTS). That's the only install.

## Run it

```
npm install
npm start
```

First `npm install` downloads Electron (~150–200MB, one-time). Three.js is
vendored locally, so gameplay runs fully offline after that. Procedural
audio and TTS also work offline (TTS depends on voices already installed
on your OS).

## Build a standalone installer (optional)

```
npm run build
```

Produces a `.exe` / `.dmg` / `.AppImage` in `dist/` via electron-builder.

## Controls

- **WASD / Arrow Keys** — move
- **E** — interact (talk to NPCs, place/collect items, build layers)
- **Esc** or the gear icon — open/close Settings

## What's implemented (mapped to the spec)

| FR | Feature | Where |
|----|---------|-------|
| FR-01 | Rainwater Collection Challenge | "Water Warden" zone — place/fill/collect bucket across 3 checkpoints, 60s timer, 50L target, splash effects |
| FR-02 | Greywater Pipe-Routing Challenge | "Pipe-Fitter Pete" zone — place pipe tiles on a grid to connect sink → garden, validated by pathfinding, animated water flow on success |
| FR-03 | Filtration System Build Challenge | "Filter Fundi" zone — collect coarse sand/fine sand/gravel, install in correct order, then a real turbidity-drop simulation as water filters through |
| FR-04 | NPC Dialogue & Quest System | Each NPC gates its challenge behind a dialogue trigger, spoken aloud via TTS with a distinct voice profile |
| FR-05 | Gamification Reward System | Points per action with a 6s combo/streak multiplier (up to 3x), litres/impact feedback text, 5 badge types, audio + particle/popup + toast feedback |
| FR-06 | Player Inventory Management | Bottom-left HUD shows carried bucket / collected filtration materials / turbidity reading contextually |
| FR-07 | Post-Challenge Assessment Quiz | 3 randomly-selected questions (from a bank of 5) per challenge after each completion, with explanatory feedback |
| FR-08 | Player Profile & Auto-Save | JSON profile (progress, points, badges, quiz scores, settings) saved via Electron's main process, restored on next launch |
| FR-09 | Tutorial / First-Run Onboarding | Welcome modal shown only on first launch; Settings menu covers configuration beyond onboarding |

## Known simplifications and honest limitations

- **Voice acting**: uses offline OS text-to-speech, not recorded human
  voices — that requires either voice actors or paid cloud APIs.
- **5 of the 7 languages are draft-quality**: see the note above. Afrikaans
  and English are higher-confidence; the others need native-speaker review.
- **Online AI-assisted questions**: not wired up. It would need your own
  Anthropic/OpenAI API key (not something I can embed for you), and calling
  a cloud LLM API directly from an Electron renderer typically runs into
  CORS restrictions without a small local proxy. The online/offline
  indicator and the quiz architecture are both ready for this to be added
  later — `pickQuizSet()` in `js/game.js` is the natural place to insert an
  "try AI-generated question, fall back to the offline bank on any error"
  path.
- **Pipe "gradient" physics**: validated as *connectivity* (unbroken path,
  via breadth-first search) rather than slope/gradient physics — a
  reasonable simplification that keeps the puzzle real and functional.
- **Turbidity numbers in Filter Fundi's challenge** are illustrative (a
  starting value chosen to look like real murky river water) — the WHO
  guideline it's validated against (<5 NTU, ideally <1 NTU) is real and
  cited in both the quiz and the completion message.
- **CSV export / POPIA consent flow** for the SO2 research study: not
  implemented — this build is the interactive product, not the research
  instrument around it.
- Characters are stylised low-poly blocky humanoids, not photoreal rigged
  models — see the in-app note; this is a deliberate, honest trade-off for
  a build that needs zero art pipeline or engine install.

## Project structure

```
main.js              Electron entry point + save/load IPC handlers
preload.js            Secure bridge exposing window.api.saveProfile/loadProfile
index.html             Page shell + all UI overlay elements (HUD, minimap, settings, quiz)
style.css               UI styling
vendor/three.min.js      Vendored Three.js (offline, no CDN dependency)
js/
  i18n.js              7-language translation dictionary + t() helper
  audio.js             Procedural Web Audio SFX/ambient + Web Speech TTS
  environment.js       Trees, flowers, rural village, wandering villagers
  effects.js           Particle bursts, floating reward popups, screen flash
  game.js              Scene, characters, all 3 challenges, gamification,
                        combo/streak rewards, quest markers, quiz engine,
                        settings, minimap, save/load, main loop
```

## Extending it

Everything is plain, editable JavaScript with clear section comments — no
build step, no compiled assets. Natural next steps:

- Get the 5 draft translations reviewed by native speakers.
- Wire up the AI-assisted quiz question path with a user-supplied API key.
- Add CSV export for SO2 analysis.
- **Real skeletal rigs**: swap `createHumanoid()`'s procedural capsule/sphere
  rig for imported glTF character models once you have (or commission)
  rigged assets — Three.js's `GLTFLoader` and `AnimationMixer` drop in
  cleanly on top of the existing scene/update-loop structure; `animateRig()`
  is the single choke point that would be replaced by mixer playback calls.
- **NPC agents**: give the wandering villagers and quest NPCs actual
  goal-directed behaviour (e.g. a small finite-state or utility-AI layer
  choosing between wander/approach-player/react-to-event) instead of the
  current scripted wander loop and static dialogue triggers — `js/game.js`'s
  `registerNpcInteractable()` and `environment.js`'s wanderer update are the
  natural insertion points.
- Extend the new combo/streak system (`js/game.js`) to the pipe-routing and
  filtration challenges' per-action moments, not just collection and quizzes.
