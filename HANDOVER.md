# Handover — Sebi's 30th birthday site

Written 2026-08-29. Read this before touching anything.

## Status

| Thing | State |
|---|---|
| Repo | `alepping/sebi-30th-bday` — **public** (required for Pages on a free account) |
| Local clone | `/Users/rudi-work-book/tmp/sebi-30-bday` (dir name is `30-bday`, repo is `30th-bday` — mismatch is intentional, don't "fix" it) |
| Live URL | https://alepping.github.io/sebi-30th-bday/ — verified live, HTTP 200 |
| Pages config | Legacy build, source = `main` branch, path `/`. Enabled via API, no Actions workflow. |
| QR code | Generated, decode-verified, **printed / about to be printed** |
| Design | **Draft built 2026-08-29** — see "What the site is" below. Facts/jokes still need concretising with the user. |

## The one hard constraint: DO NOT BREAK THE QR CODE

The QR is being printed on physical things. It encodes exactly:

```
https://alepping.github.io/sebi-30th-bday/
```

The QR contains *only* that URL, so **redesigning the site freely is safe** — content, layout, CSS, adding pages, rewriting `index.html` from scratch all keep the code valid.

What **would** break every printed copy, irreversibly:

- Renaming the repo away from `sebi-30th-bday`
- Renaming / deleting the GitHub account `alepping`
- Making the repo private (Pages goes 404 on a free account)
- Disabling Pages, or removing `index.html` from the repo root
- Adding a custom domain **without** keeping the `github.io` URL working
- Moving the homepage into a subdirectory

If the user asks for any of the above, stop and warn them that printed codes are already out.

## Repo contents

```
index.html          holding page (German) — dark bg, gold accent, serif. Placeholder, meant to be replaced.
.nojekyll           stops Jekyll processing; keep it
HANDOVER.md         this file
qr/
  sebi-30-qr.svg    vector, for print
  sebi-30-qr.eps    vector, for print shops
  sebi-30-qr.png    1800x1800, digital use
  URL.txt           the exact encoded string
```

QR params used, if it ever needs regenerating identically:
```
qrencode -t SVG -l H -m 4 -s 10 -o qr/sebi-30-qr.svg "$(cat qr/URL.txt)"
```
(`-l H` = 30% error correction, so it tolerates damage and a logo over the centre. `qrencode` was installed via Homebrew in that session.)

Verify any regenerated QR by decoding it, don't eyeball it. A Swift/Vision one-liner works on macOS; `VNDetectBarcodesRequest` on the PNG returned the exact URL above.

## Deploy workflow

Plain git push. No build step, no framework, no dependencies — keep it that way unless the user asks otherwise.

```
git add -A && git commit -m "..." && git push origin main
```

Live in ~30–60s. Check with:
```
curl -s -o /dev/null -w '%{http_code}' https://alepping.github.io/sebi-30th-bday/
gh api repos/alepping/sebi-30th-bday/pages/builds/latest --jq .status
```

`gh` is authenticated as `alepping` (ssh for git ops). Token scopes: `repo`, `gist`, `read:org`, `admin:public_key` — note **no `workflow` scope**, so it cannot push a GitHub Actions workflow file. Stick with the legacy branch build.

## What the site is (decided 2026-08-29)

**Not a party invite.** It is a gift-reveal experience for Sebi himself, in English. Flow:

```
intro → q1 → q2 → q3 (meaningless joke choices, each with a snarky reaction)
      → choose (the real fork)
      → iq → iq1..iq5 (IQ calibration: sudoku, E?mc², E²=(pc)²+?, 1/31, mahjong) → iq-score
         ├── kart  → kart-place  → kart-plan  → kart-fine  → (link to stars)
         └── stars → stars-place → stars-plan → stars-fine → (link to kart)
```

- Hash-routed single `index.html`; browser back/forward works; every screen has Back + an "Options" link to the fork. Nothing is recorded — he tells the group in person.
- Three themes on `body[data-theme]`: `calm` (ivory, Fraunces serif) for intro/questions, `kart` (black, red/yellow, Barlow Condensed italic, checkered strips, speed lines) and `stars` (navy, gold, twinkling canvas starfield).
- Sebi's face: `assets/sebi-head.png` is a Vision person-segmentation cutout of `sebi_photo.jpg` (source photo is **gitignored**, stays local). Composited via inline SVG into a racing helmet (kart hero) and a space helmet (stars hero). `assets/sebi-bust.png` is unused so far.
- Photos: `assets/kart-track.jpg` (Wikimedia CC0, generic) and `assets/westhavelland-night.jpg` (Wikimedia, Anphex, CC BY-SA 4.0 — credit is in the caption, keep it).
- Yellow `<span class="todo">` markers in the page = facts the user still has to supply (package choice, number of guests, dinner, overnight). Search `class="todo"`.
- Warm-up questions q1–q5 are the user's own content — don't rewrite them.
- Result-screen rules: 0/5 is a dead end (`sebi30-dead` in sessionStorage forces the result screen for the rest of that tab — a fresh QR scan opens a new tab/session); 2/5 only offers "Start from the very beginning"; 3/5 only "Retake"; 4–5/5 proceed.
- Easter egg: `robots.txt` disallows `/last_dance.html` (that's the only hint). `last_dance.html` shows a hooded figure asking for a number 1–100; **37** reveals the Yoshi egg (3 taps to crack, girlfriend portrait `assets/egg-face.png` + "Oh Behbi'", tapping her opens `assets/egg-original.jpg`); any other number shows "No." and redirects to the start. The check is `(n*7919)%1000===3`, so 37 isn't in the source verbatim. Preview: `last_dance.html?stage=3`.
- Fork page: once both `kart-fine` and `stars-fine` have been visited (sessionStorage `sebi30-seen`), a third card "Option ? — The easter egg" appears; tapping it only says it wouldn't be an easter egg if it were that easy. Preview: `index.html?seen#choose`.
- No directory listings anywhere (removed on purpose). Source photos `girlfriend_*.jpg` are gitignored. **HANDOVER.md itself is served publicly** — it spoils the whole gift; consider renaming it to something boring.
- IQ block: answers live in `sessionStorage` (`sebi30-iq`); score tiers 5→200, 4→150, 3→100, 2→50, 1→20, 0→0 with a Sebi illustration each (`assets/sebi-head-soft.png`, feathered cutout). Preview a tier without answering: `index.html?iq=4#iq-score`.

### Research facts used (verified 2026-08-29)
- Berlin Kart: Werbellinstraße 50, 12053 Berlin, U8 Boddinstraße, indoor ~400 m (site still claims 752 m in places), 6.5 hp Sodi karts, lap timing on monitors, packages €22 (11 min) – €77 (66 min endurance), open daily. https://www.berlin-kart.de/
- Sternenpark Westhavelland: IDA Dark Sky Park Feb 2014, 1,380 km², ~21.7 mag/arcsec² (Bortle ~3), Gülpe "darkest place in Germany". Guides: Thomas Becker (zumnordlicht.com — observation evenings Astronomie-Labor Kleßen €10, telescope course €45, night walk €15; most dates waitlisted), Marion Werner Strodehne (star walks, 1.5–2 h). RE4 rail line closed Oct 2026–Dec 2027 — go by car.

### Preview without deploying
Headless Firefox at phone width, with reduced motion so the capture is the final state:
```
echo 'user_pref("ui.prefersReducedMotion", 1);' > /tmp/ffprof/user.js
/Applications/Firefox.app/Contents/MacOS/firefox --headless --no-remote --profile /tmp/ffprof \
  --window-size=390,844 --screenshot out.png "file://$PWD/index.html#kart"
```

## Old open questions (mostly moot now)

The user was asked these and interrupted before answering, so **all of it is still unknown.** Nothing about the party itself has been established — there are no confirmed facts, and the holding page contains none.

1. **Visual direction.** Options floated: dark & elegant (what the placeholder does); bright & playful; photo-led with a hero image of Sebi; minimal typographic. No preference expressed.
2. **Sections.** Candidates: Wann & Wo + maps link; RSVP; Ablauf/schedule; Geschenke, dress code, parking, accommodation.
3. **Language.** Placeholder is German, assumed from context — *not confirmed.*
4. **The actual party facts.** Date, time, venue, address — all unknown. Must come from the user.

### RSVP — not needed (no invite)
It's a static site, so there is no backend. Realistic options, roughly in order of how well they fit:
- `mailto:` or a WhatsApp deep link — zero infrastructure, works immediately
- Embedded Google Form — real collected responses, but an ugly iframe
- A form service (Formspree etc.) — needs an account and puts a third party in the loop

### Privacy
The repo is public and the source is browsable. Fine for a venue name and a date. Flag it to the user before putting a **home address, phone numbers, or guest names** on the page — that was raised once and never resolved.

## Notes

- Mobile-first is not optional. Essentially every visitor arrives by scanning the QR with a phone.
- Keep the page a single self-contained `index.html` with inline CSS if possible. It makes the whole thing trivially reviewable and impossible to break at deploy time.
- The holding page is genuinely disposable. Don't feel obliged to preserve its design.
