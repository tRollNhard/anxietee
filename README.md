# Anxietee 💜

**Calm, right now.** A free, private, no-login web app that helps people get through an anxious moment and build calmer habits — using evidence-based coping tools, honest "wellness not treatment" framing, and always-visible crisis links.

> Built by THATdudeAGAIN (Jason W. Clark) to help others. Client-side only — nothing a user does ever leaves their device.

## Guiding rules
1. **Free & private forever** — no accounts, no paywall, no tracking, no data leaves the device.
2. **"Support / wellness," never "treatment"** — keeps Anxietee out of medical-device / AI-therapy regulation.
3. **Retention is the whole game** — forgiving design, no streak-shame, a great first-use moment.

## Status — Phase 1 (shipped)
Single self-contained `index.html` (plain HTML/CSS/JS, no build step — same style as the tools site).

- **Calm-Now** — the hero flow: reassurance → 5-4-3-2-1 grounding → guided breathing → a kind closing.
- **Breathe** — standalone paced-breathing orb with 3 patterns: Calm (6/min), Box (4-4-4-4), 4-7-8.
- **Grounding** — the 5-4-3-2-1 senses exercise, reachable on its own.
- **Get help now** — always-visible crisis links: 988 (call/text), Crisis Text Line 741741, 911, findahelpline.com.
- **First-run welcome** — states privacy + "not treatment" + crisis access up front.
- **Design** — dark base with purple→cyan→pink gradients and slow drifting blobs (THATdudeAGAIN palette, calm treatment). Respects `prefers-reduced-motion`. PWA-ready (`manifest.json`; add icons + service worker in a later phase).

## Status — Phase 2 (shipped)
All local-only, no data leaves the device. Verified working in preview (no console errors).
- **Reframe (CBT thought record)** — 5-step guided form (situation → anxious thought + belief % → evidence for → evidence against → balanced thought + re-rated belief %). Saves entries to `localStorage` (`anxietee_thoughts`), lists past entries with belief-shift (before → after), per-entry delete.
- **Check-in (GAD-7)** — the validated 7-item scale, 0–3 each, score 0–21 with severity band (minimal/mild/moderate/severe) + supportive non-diagnostic messaging. Saves to `localStorage` (`anxietee_checkins`) and draws an inline SVG **trend chart** (gradient line, dashed marker at 10).
- **Your data** — footer link; shows counts, **Export my data** (downloads `anxietee-data.json`), **Clear all my data** (confirm → wipes). Escapes user input; keeps `anxietee_seen`.
- Home tiles updated: Breathe · Grounding · Reframe · Check-in · Get help · Learn (soon).

## Status — Phase 3 (shipped)
Verified working in preview (no console errors; service worker active with all assets cached).
- **Learn library** — 18 plain-language articles wired in from the two research projects:
  - *Understanding anxiety* (13 topics from the "Understanding Anxiety" report): the science, severity/GAD-7, GAD, panic attacks, panic disorder, social anxiety, phobias, agoraphobia, separation anxiety, selective mutism, OCD, PTSD, illness anxiety. Stat-chip callouts for key numbers.
  - *What can help* (5 topics condensed from the treatments evidence outline): talk therapies, medication landscape, mind-body practices, lifestyle & supplements, new/emerging — each item carries an honest evidence tag (First-line / Strong / Moderate / Limited / Experimental) plus safety flags (benzos, kava, ashwagandha, CBD). Framed strictly as education ("delivered by professionals / talk with a clinician"); specific dosages deliberately omitted.
  - Cross-links into the app's own tools (Breathe, Check-in, Reframe); every article ends with an education-only disclaimer + sources line.
- **Full PWA / offline** — `icon-192.png` + `icon-512.png` (generated orb artwork matching the app), `sw.js` service worker (network-first for navigations so updates land, cache-first for assets), registered from `index.html`. App shell fully cached — works offline after first visit.
- Home tile **Learn** is now live ("Soon" badge removed).

## Security (hardened & verified)
- **Content-Security-Policy** (meta tag): `default-src 'none'` baseline — no external scripts, styles, images, fonts, or connections can ever load. This *enforces* the "nothing leaves your device" promise at the browser level. Verified live: external `fetch()` and external `<script>` injection are both blocked.
- **XSS**: the only user-input → HTML path (reframe list) goes through `esc()` (now escapes `& < > " '`). Verified live with hostile payloads (`<script>`, `<img onerror>`, attribute-breakout) — all render as inert text, nothing executes. Stored dates/belief numbers are escaped/coerced too, so even hand-tampered localStorage can't inject markup; non-array tampered storage is discarded.
- **Clickjacking**: JS frame-buster (static hosts like GitHub Pages can't send `frame-ancestors`/`X-Frame-Options` headers).
- **Referrer**: `no-referrer` meta.
- **Service worker**: only handles same-origin http(s) GETs; cache writes guarded; cache `anxietee-v5`.
- No external links with `target=_blank` (no tab-nabbing surface), no cookies, no third-party anything.

## Code review pass (July 8 2026 — all findings fixed & re-verified)
- **Fixed (critical):** duplicate `#orb`/`#phase`/`#count`/`#breatheToggle` IDs — the Calm-Now flow's injected breathing markup persisted and hijacked `getElementById`, freezing the standalone Breathe view afterward. `go()` now clears `#calm-inner` when leaving the calm view.
- **Fixed (race):** `startBreathing` null-guards its elements; the calm flow's delayed auto-start timer is tracked (`bStartTimer`) and cancelled by `stopBreathing`.
- **Fixed (a11y):** footer legal links are real `<button>`s (keyboard-focusable); welcome modal has `role="dialog"`/`aria-modal`/`aria-labelledby` + takes focus; `go()` moves focus to the active view.
- **Fixed (misc):** removed `"medical"` from manifest categories (conflicted with wellness-not-medical positioning); `exportData` revokes its blob URL; SW offline fallback degrades gracefully; dead CSS (`.tile.soon`, `.badge`) and dead `calmStage` removed.

**Second pass (same day — all fixed & verified):** `prefers-reduced-motion` now fully honored (`REDUCED_MOTION` flag: instant scroll instead of smooth; orb falls back to the stylesheet's 0.4s transition instead of 4–8s inline animation — pacing still readable via labels/counts); `.view:focus{outline:none}` (view containers, not controls); GAD-7 option buttons start with `aria-pressed="false"`; `dismissWelcome` moves focus to the home view; grounding progress bars `aria-hidden`.

## Roadmap
- **Phase 4 (careful):** personalization, more content. **No AI chatbot** unless properly red-teamed for crisis safety.

## Evidence basis (Phase 1)
- Paced breathing (~6 breaths/min) and 5-4-3-2-1 grounding are established acute-anxiety techniques.
- No efficacy/medical claims are made anywhere in the UI — framing is supportive only.

## Legal (added Phase 1.5)
- **Terms of Use** and **Privacy Policy** pages are built into `index.html` (footer links → `go('terms')` / `go('privacy')`), plain-language and tailored to the app's actual design (wellness-not-treatment, not-for-emergencies, no-data-collection).
- Contact email is filled in (jwclarkladymae@gmail.com). Optional on deploy: switch to a Formspree contact form to avoid exposing the raw address.
- These are solid starter templates that follow the responsible-design playbook — for full peace of mind if the app grows or gets monetized, have a lawyer glance at them once. Not legal advice.

## To do before public launch
- [x] Fill in the contact email in Terms + Privacy (done in Phase 1.5).
- [x] Add `icon-192.png` / `icon-512.png` for PWA install (done in Phase 3).
- [x] Hosting: own GitHub Pages repo — **live at https://trollnhard.github.io/anxietee/** (repo tRollNhard/anxietee, deployed July 8 2026).
- [ ] Optional custom domain.
- [ ] Accessibility pass (screen-reader labels, contrast) — largely in place.
- [ ] Optional: lawyer review of Terms/Privacy before wide/monetized launch.

## Run locally
`python -m http.server 4180 --directory C:/Users/THATdudeAGAIN/Anxietee` then open http://localhost:4180
