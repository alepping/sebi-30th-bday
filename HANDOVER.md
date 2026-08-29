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
| Design | **Not started.** Only a holding page exists. This is the next session's job. |

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

## Open design questions — ask these first

The user was asked these and interrupted before answering, so **all of it is still unknown.** Nothing about the party itself has been established — there are no confirmed facts, and the holding page contains none.

1. **Visual direction.** Options floated: dark & elegant (what the placeholder does); bright & playful; photo-led with a hero image of Sebi; minimal typographic. No preference expressed.
2. **Sections.** Candidates: Wann & Wo + maps link; RSVP; Ablauf/schedule; Geschenke, dress code, parking, accommodation.
3. **Language.** Placeholder is German, assumed from context — *not confirmed.*
4. **The actual party facts.** Date, time, venue, address — all unknown. Must come from the user.

### RSVP needs a decision
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
