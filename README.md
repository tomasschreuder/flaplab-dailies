# FlapLab daily queue

Public repo of **dated game packages**. The private `flapdiy` Devvit app fetches `dailies/YYYY-MM-DD/` from `main` each morning (08:00 Europe/Amsterdam) and posts **one** official game as **FlapLab Daily**.

Grok Bot fills this repo **once a week** (Sunday 18:00 Amsterdam): seven future folders, soonest dates first. The cron does not care about the bot — it only reads **today’s** folder.

If you are the bot: read this file for the **package contract**. Read `BOT.md` (if present) for taste, slots, physics, and scouting. If they disagree on files/JSON/formats, **this README wins**.

## Layout

```
README.md
BOT.md                   # optional; full Grok prompt copy
do-not-use.md            # content + bad PNG hosts
evergreen.md             # fallback concepts
dailies/
  2026-09-06/
    game.json            # required
    bird.png             # or bird.gif — name must match game.json
    pillar.png           # or pillar.gif
    sky.png              # or sky.gif
    pillar2.png          # optional, png or gif
```

One folder per Amsterdam date. No nested dirs. No files the JSON does not name (except you may keep nothing extra — extra files are ignored by the cron, but do not add them).

## What the cron fetches

```
https://raw.githubusercontent.com/tomasschreuder/flaplab-dailies/main/dailies/{YYYY-MM-DD}/
```

Invalid JSON, missing files, non-PNG/GIF magic bytes, or bird/pillar **without real alpha** → that morning is **skipped**. There is no retry with guessed art.

## game.json (required)

```json
{
  "title": "FlapLab Daily — Be ze baguette",
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
| `title` | 1–80 chars, starts with `FlapLab Daily — ` |
| `bird` `pillar` `background` `pillar2` | Same-folder filename only. `^[a-zA-Z0-9._-]+\.(png\|gif)$`. No URLs, no `..`, no spaces |
| `pillar2` | Omit unless the file exists. Then you may set `pillar2OnTop` |
| `settings` | Optional. Only the keys above; others dropped. Missing keys → engine defaults |
| `slot` `story` `inspiration` `repostHints` | Optional for the game server (ignored). **Required for the bot** so a later cross-poster can work |

### Settings — defaults and bot target band

Engine defaults: gravity `0.36`, jumpForce `6`, scrollSpeed `3.5`, birdSize `65`, pillarGap `175`, pillarSpacing `280`, `pillarProportional` true, `pillar2OnTop` false, `birdControlMode` `"flap"`, ground `#4d2d08`, grass `#00a822`.

Server clamp: gravity 0.02–1.2, jumpForce 2–20, scrollSpeed 0.5–12, birdSize 10–180, pillarGap 40–420, pillarSpacing 80–700, mode `"flap"` \| `"fly"`, colors `#RRGGBB`.

Bot should stay ~±30% of defaults unless slot is `fun` (space / jet). Flap apex `jumpForce² / (2 × gravity)` target **80–180**. Fly: `jumpForce ≥ 4.8 × gravity`. Gap ~**2.5×** birdSize. `pillarSpacing / scrollSpeed` ≥ **40**. Do not copy defaults blindly (default apex is too low).

## Images

| Role | Format | Alpha | Size aim |
|---|---|---|---|
| Bird | `.png` or `.gif` | **Required** around subject | ~256×256, PNG under 300KB (cap 400KB, 512×512). GIF under 2.5MB |
| Pillar / pillar2 | `.png` or `.gif` | **Required** | ~128×256 or 160×400, PNG under 300KB (cap 400KB, 512×1024). GIF under 2.5MB |
| Background | `.png` or `.gif` | Opaque OK | ~400×650, PNG under 600KB (cap 800KB, 1280×1920). GIF under 3.5MB |

- **No JPEG / WebP in the folder.** Convert a JPEG sky to PNG first.
- Subject fills most of the canvas. Fake transparency (white box) → skip that morning.
- GIF: short loop, subject does not explode to fill the frame (crop is union of frames). Server keeps ≤40 frames.
- No text the player must read to play.

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

Never edit private `flapdiy`. Never overwrite a finished date.
