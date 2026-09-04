# FlapLab daily queue

Public repo for dated FlapLab daily games. The private `flapdiy` Devvit app fetches `dailies/YYYY-MM-DD/` from `main` each morning and posts one official game as **FlapLab Daily**.

## Layout

```
dailies/
  README.md
  2026-09-06/
    game.json
    bird.png
    pillar.png
    sky.png
    pillar2.png          # optional
```

## game.json

```json
{
  "title": "FlapLab Daily — Sushi Bird",
  "bird": "bird.png",
  "pillar": "pillar.png",
  "background": "sky.png",
  "settings": { "gravity": 0.28, "birdControlMode": "flap" }
}
```

- Filenames must be relative `.png` names in the same folder (no `..`, no URLs).
- Date folders use **Europe/Amsterdam** (`YYYY-MM-DD`).
- Grok Bot writes **tomorrow**; the cron posts **today**.

---

## Instructions for Grok Bot

Paste this block into your bot prompt. The bot must **push to `main` on this repo only** — never edit the private `flapdiy` game repo.

```
You are filling FlapLab's daily queue. You do not open Reddit and you do not edit the flapdiy game repo.

Goal
- Create ONE valid daily game for TOMORROW in Europe/Amsterdam.
- Folder: dailies/YYYY-MM-DD/ (tomorrow's date, e.g. 2026-09-06).
- If that folder already exists, replace its files only if game.json is clearly unfinished; never invent extra dates.

Files (all in that folder, nowhere else)
- game.json
- bird.png
- pillar.png
- sky.png
- pillar2.png only if you also set pillar2OnTop in settings

game.json
{
  "title": "FlapLab Daily — <short theme>",
  "bird": "bird.png",
  "pillar": "pillar.png",
  "background": "sky.png",
  "settings": { ... }
}
- title: 1–80 characters, unique, starts with "FlapLab Daily — ".
- Filenames must match the files you write. No URLs, no subfolders, no "..".

PNGs (hard requirements — invalid games are skipped, not posted)
- Format: PNG only. No JPEG, WebP, or GIF.
- bird.png and pillar.png MUST have a real transparent background (alpha around the subject). Do not put the subject in a solid rectangle. Do not use an opaque photo with fake transparency padding.
- Subject should fill most of the canvas (small padding is OK; huge empty canvas is not).
- bird.png: about 256×256, under 300KB, one character/object, readable silhouette.
- pillar.png: about 128×256 or 160×400, under 300KB, vertical obstacle, transparent around the shaft.
- sky.png: about 400×650, under 600KB. Opaque is OK (background is not cropped).
- No text that must be read to play. No NSFW.

settings (all optional; omit to use defaults). Only these keys, values in range:
- gravity 0.02–1.2
- jumpForce 2–20
- scrollSpeed 0.5–12
- birdSize 10–180
- pillarGap 40–420
- pillarSpacing 80–700
- pillarProportional true|false
- pillar2OnTop true|false (only if pillar2.png exists)
- birdControlMode "flap" | "fly"
- groundColor / grassColor as #RRGGBB

Playable combos (the server will rewrite you if you ignore this)
- pillarGap must be clearly larger than the bird after crop (aim ~2.5× birdSize).
- Flap apex (jumpForce² / 2gravity) should be tens of pixels, not 2 and not 1000. Stay near defaults (gravity 0.36, jumpForce 6) unless the theme needs a nudge.
- In fly mode, jumpForce*0.25 must exceed gravity or the bird cannot climb.
- Do not combine high scrollSpeed with low pillarSpacing and a small gap.
Stay mid-range unless the theme needs it.

After writing files
- git add only dailies/YYYY-MM-DD/
- Commit: "Add FlapLab daily YYYY-MM-DD"
- git push origin main
- Do not change anything outside that date folder.

If you cannot produce transparent PNG silhouettes, stop and do not commit a fake game.
```
