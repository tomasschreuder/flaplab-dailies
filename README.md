# FlappyLab daily queue

Public repo of **dated game packages**. The private `flapdiy` Devvit app fetches `dailies/YYYY-MM-DD/` from `main` each morning (08:00 Europe/Amsterdam) and posts **one** official game as **FlappyLab Daily**.

Grok Bot fills this repo **once a week** (Sunday 18:00 Amsterdam): seven future folders, soonest dates first. The cron does not care about the bot — it only reads **today’s** folder. If today’s folder is missing or broken, the cron posts a hand-made package from `evergreen/` instead, so no morning is ever empty.

If you are the bot: read this file for the **package contract**. Read `BOT.md` (if present) for taste, slots, physics, and scouting. If they disagree on files/JSON/formats, **this README wins**.

## Layout

```
README.md
BOT.md                   # optional; full Grok prompt copy
do-not-use.md            # content + bad PNG hosts
evergreen.md             # fallback *concepts* for the bot (ideas, not files)
dailies/
  2026-09-06/
    game.json            # required
    bird.png             # or bird.gif — name must match game.json
    pillar.png           # or pillar.gif
    sky.png              # or sky.gif
    pillar2.png          # optional, png or gif
evergreen/               # complete fallback packages — humans only, bots never edit
  index.json             # which packages exist (+ optional months)
  flappy-cat/            # same file contract as a dated folder
  ...
```

One folder per Amsterdam date. No nested dirs. No files the JSON does not name (except you may keep nothing extra — extra files are ignored by the cron, but do not add them).

## What the cron fetches

```
https://raw.githubusercontent.com/tomasschreuder/flaplab-dailies/main/dailies/{YYYY-MM-DD}/
```

Invalid JSON, missing files, non-PNG/GIF magic bytes, or bird/pillar **without real alpha** → that folder is **skipped** and an evergreen package is posted instead. There is no retry with guessed art.

## game.json (required)

```json
{
  "title": "Flappy Baguette: Be Ze Baguette",
  "bird": "bird.png",
  "pillar": "pillar.png",
  "background": "sky.png",
  "pillar2": "pillar2.png",
  "settings": {
    "gravity": 0.28,
    "jumpForce": 7,
    "scrollSpeed": 3.2,
    "birdSize": 62,
    "pillarGap": 180,
    "pillarSpacing": 290,
    "pillarProportional": true,
    "pillar2OnTop": false,
    "birdControlMode": "flap",
    "groundColor": "#4d2d08",
    "grassColor": "#00a822"
  },
  "slot": "country",
  "story": "You are a baguette. The Eiffel Tower is trying to stop you.",
  "inspiration": "Evergreen country-food meme.",
  "repostHints": ["r/france", "r/food", "r/FlappyLab"]
}
```

| Field | Rule |
|---|---|
| `title` | The **caption only**, 1–62 chars. The server appends ` \| FlappyLab Daily` (posted title: `Flappy Baguette: Be Ze Baguette \| FlappyLab Daily`). Name the subject in searchable words — prefer `Flappy <Subject>: <hook>`. Legacy `FlapLab Daily — …` titles still work (the prefix is stripped). Over-long captions are trimmed at a word boundary |
| `bird` `pillar` `background` `pillar2` | Same-folder filename only. `^[a-zA-Z0-9._-]+\.(png\|gif)$`. No URLs, no `..`, no spaces |
| `pillar2` | Omit unless the file exists. Then you may set `pillar2OnTop` |
| `settings` | Optional. Only the keys above; others dropped. Missing keys → engine defaults |
| `story` | Optional for the server, **required for the bot**: 1–2 sentences, ≤ 400 chars. Becomes the post's text description (what Reddit search and Google can read) |
| `slot` `inspiration` `repostHints` | Optional for the game server (ignored). **Required for the bot** so a later cross-poster can work |

### Settings — defaults and what the server allows

Engine defaults: gravity `0.36`, jumpForce `6`, scrollSpeed `3.5`, birdSize `65`, pillarGap `175`, pillarSpacing `280`, `pillarProportional` true, `pillar2OnTop` false, `birdControlMode` `"flap"`, ground `#4d2d08`, grass `#00a822`.

Server clamp: gravity 0.02–1.2, jumpForce 2–20, scrollSpeed 0.5–12, birdSize 10–180, pillarGap 40–420, pillarSpacing 80–700, mode `"flap"` \| `"fly"`, colors `#RRGGBB`.

**Extreme is welcome.** Pick one of the twelve physics archetypes in `BOT.md` §6 per day (Speed Demon, Moon Gravity, Brick, Needle, Giant, Tiny, Gauntlet, Twitchy, Afterburner, Hover, Chaos, Classic) and vary them across the week. The server only repairs settings that are **impossible**: a gap too small to thread at that speed and gravity, fly thrust weaker than gravity (`jumpForce × 0.25 ≤ gravity`), a flap that always hits the lethal ceiling (apex `jumpForce² / (2 × gravity)` over 340), pipes closer than 0.2 s, or height swings no tapping could climb. Anything merely brutal ships exactly as written. Needle, Tiny and Gauntlet days need `pillarProportional: false` (proportional pipes get very wide around small gaps).

## Images

| Role | Format | Alpha | Size aim |
|---|---|---|---|
| Bird | `.png` or `.gif` | **Required** around subject | ~256×256, PNG under 300KB (cap 400KB, 512×512). GIF under 2.5MB |
| Pillar / pillar2 | `.png` or `.gif` | **Required** | ~128×256 or 160×400, PNG under 300KB (cap 400KB, 512×1024). GIF under 2.5MB |
| Background | `.png` or `.gif` | Opaque OK | ~400×650, PNG under 600KB (cap 800KB, 1280×1920). GIF under 3.5MB |

- **No JPEG / WebP in the folder.** Convert a JPEG sky to PNG first.
- Subject fills most of the canvas. Fake transparency (white box) → that folder is skipped.
- The **top** of the pillar image faces the gap (the top pipe is drawn flipped) — put caps, snow, flaps at the top.
- GIF: short loop, subject does not explode to fill the frame (crop is union of frames). Server keeps ≤40 frames.
- No text the player must read to play.

## Evergreen pool (`evergreen/`)

Complete packages with the exact same file contract as a dated folder, listed in `evergreen/index.json`:

```json
{ "packages": ["flappy-cat", { "slug": "flappy-santa", "months": [11, 12] }] }
```

- Used **only** when `dailies/<today>/` is missing or fails validation. The server picks the least recently used package, preferring ones whose `months` include today.
- Original art only (no IP), searchable `Flappy <Thing>` titles, and a different physics archetype per package so the fallback days feel as varied as the real ones.
- Generated and validated by `flapdiy/scripts/evergreen` (runs the real server pipeline). Humans only — **bots never edit `evergreen/`**.

## Content safety

No **nudity**, no **drugs** (product/use/paraphernalia as the joke), no **gore**. Franchise / IP skins are in bounds.

## Weekly bot fill

Schedule: **Sunday 18:00 Europe/Amsterdam**. Targets: tomorrow `D` through `D+6`. Skip folders that are already complete. Push **one date at a time**.

Slots by offset from `D` (not by weekday): `trending`, `meme`, `fun`, `recognisable`, `cat`, `country`, then `political` (even ISO week of that date) or `story` (odd).

```
git add only dailies/YYYY-MM-DD/
git commit -m "Add FlapLab daily YYYY-MM-DD"
git push origin main
```

Never edit private `flapdiy`. Never overwrite a finished date. Never edit `evergreen/`.
