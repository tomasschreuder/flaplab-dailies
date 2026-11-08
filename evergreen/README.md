# Evergreen FlappyLab Dailies

Complete, ready-to-post daily packages. The game posts one of these **only** when `dailies/<today>/` is missing or broken (for example when the weekly bot run fails), so the community never wakes up to an empty morning.

- Original pixel art, no IP. Titles use the searchable `Flappy <Thing>: <hook>` format.
- `index.json` lists the pool. Optional `months` mark seasonal packages (Santa in Nov–Dec, etc.). The server picks the least recently used package, in season first.
- Every package is generated **and validated with the real server pipeline** by `flapdiy/scripts/evergreen/generate.ts`. Do not hand-edit the PNGs; change the art in that script and regenerate.
- Bots must never edit this folder.

| Package | Physics archetype (BOT.md §6) | Season |
|---|---|---|
| flappy-cat | Classic | all year |
| flappy-rocket | Afterburner — hold to fly, speed 7.5 | all year |
| flappy-ghost | Moon Gravity — gravity 0.08 | Sep–Nov |
| flappy-santa | Gauntlet — chimneys every 150 px | Nov–Dec |
| flappy-penguin | Brick — gravity 0.95, jump 13.5 | Nov–Feb |
| flappy-pizza | Tiny — 24 px slice, 60 px gaps, speed 6 | all year |
| flappy-shark | Giant — 150 px shark | all year |
| flappy-duck | Needle — 64 px gaps at speed 8 (HARD) | all year |
| flappy-ufo | Hover — drifty hold to fly | all year |
| flappy-chicken | Twitchy — 24 px hops, spam the tap | all year |
