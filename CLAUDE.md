# Direborn Company Profile — project notes

Animated, bilingual (EN/ID) web deck for Direborn Esports and its tournament product, Dire Battle.
It is a company profile for prospective brand partners, not a priced sponsorship pitch.

## How it is built

- Static site, no build step: `index.html` (all CSS and JS inline) plus `assets/`.
- Deploy target: Vercel, team **DRBN**, from `DirebornEsports/direborncompo` (`main`).
  Framework preset "Other", no build command. **The Vercel project has not been created yet**
  (the connected account got 403 on project creation; the repo must be imported once from the dashboard).
- `index.html` in this repo is the source of truth from here on.

## Layout system

- Every slide is a `<section class="slide">` holding a fixed 16:9 stage `.st` (100rem x 56.25rem).
  `html{font-size:min(1vw,1.7778vh)}`, so **1rem = 1% of stage width** and everything scales like a real deck.
  Size things in rem; small text uses `max(px, rem)` floors.
- Phones / portrait (`max-width:820px` or portrait aspect): slides stack and scroll; see the media query.
- Each slide has, at the same stage position: an eyebrow label, a headline, a corner chapter mark.
  These carry `data-vt="eyebrow|title|mark|dbmark"` and are morphed between slides with the
  View Transitions API (`go()` in the script). Elements shared by both slides get class `vt-on`,
  which disables their own entrance animation.
- Chapters via `data-ch`: 0 opening, 1 Direborn (red), 2 Dire Battle (green accent), 3 partnership.
- Backdrop: WebGL fragment shader on `#fx` (smoke + embers), tinted by chapter, pulses on slide change.
- Language: `<span class="en">` / `<span class="id">` pairs, toggled by `html[lang]`.
  Numbers use `data-n` (static), `data-count` (count-up), `data-m` (month) and are formatted per locale.
  Do not reuse `data-n` for anything else (it once overwrote the roster photos).
- Navigation: arrows / space / swipe, per-slide hash links (`#circuit`), `F` for fullscreen.
  Bottom bar has a progress rail (`#segs`, one clickable segment per slide, grouped by chapter); the slide navigator
  opens only from the "All slides" button (hover-to-open was removed).
- Depth and motion: `.st[data-no]::after` is the ghost slide number; `--mx/--my` on `.deck` drive cursor parallax;
  `--dx` (1 forward, -1 back) makes entrances travel in the direction of navigation; `.way` cards tilt (`.tilt`);
  posters in `.strip` open in `#lightbox`.
- Sound: `#bgm` music loop plus three cues in `assets/audio/`, mapped in `slideSound()`: `sfx-whoosh` on slide change,
  `sfx-deep` instead when the chapter (`data-ch`) changes, `sfx-impact` on the manifesto and Dire Battle reveals.
  No hover, click or navigator sounds. Sources and licences: `assets/audio/credits.txt`.
  On by default, starts on the first interaction, remembered in localStorage.

## Slides (17)

cover, manifesto, who, engines, roster, record, direbattle, circuit, events, cup, uprising,
reach, content, production, roadmap, partner, contact

## Decisions already made by the owner (keep these)

- Player IDs, exact spelling: Dreamocel, woMy, PotaT2, db-, dalul.
  Photo files are named the wrong way round: **db- uses `dalul.webp` (sitting), dalul uses `db.webp` (pointing)**.
- Cover tagline is just "Indonesian Dota 2." Manifesto: "Born to Regenerate. Directed to Dominate."
- Manifesto mark assembles from four shards; no shadow-trail effect; no text-scramble effect anywhere.
- No wipe/stinger between slides; transitions must feel seamless (shared elements glide).
- Track record title is "Our first year."; the champion post is leftmost and larger in each row.
- Events table order: Showdown 1v1 Mid sits before Dire Battle Cup.
- "Delayed player cams" was removed from the production slide.
- Hovering near the next/prev buttons must not open the slide navigator.
- Data must be exact and unchanged; highlight a stat only when it is genuinely notable.
- Whiteboard "brand vision" numbers are old and must not be used (its roadmap steps are fine).

## Data sources

- Dire Battle events, teams, prize pools, champions: direborn.team/tournaments.
- YouTube (Cup content, 12 Oct – 8 Nov 2026) and Instagram (1 Jun – 9/10 Nov 2026): owner's analytics screenshots.
- Track record: EPL SEA 19 Grand Champion; Lunar Horse Trophy champion (Grand Final 3–1 vs Mentality Monsters).

## Open items

- Contact slide still has placeholders: `[Name · Phone]`, `[email]`.
- Sound effects: the uisfx UI cues the owner found "weird" were removed (10 Nov 2026). Replaced by three recorded
  sounds found through Openverse (Freesound, all CC0), trimmed and peak-normalized to -3 dB. Owner has not heard them yet.
- Music `assets/audio/bgm.mp3` is an 88s loop cut from a lo-fi "timer" track; licence for public use unconfirmed.
- Missing data: team counts for Mix Series and Showdown 1v1 Mid, Mix Series champion, EPL SEA 19 final opponent/score.
- Not on slides yet: player roles, follower counts per platform, audience demographics.
- Motion and audio have been syntax-checked and screenshot-tested (1440x810 and 390x844) but not watched or heard.

## Testing

Playwright + Chromium work headless. Load `index.html#<slide-id>`, wait ~2.5s for entrances, screenshot.
Check `.slide.active` for vertical overflow at 1440x810.
