---
name: brain-polish
description: Use when the user says "brain polish", "make Brain consistent", "fix the drift", or types /brain-polish, or after adding or changing UI in the Brain app (~/Notes/.brain/mobile/index.html or app/index.html). Finds controls, colours and motion that don't follow Brain's own rules and fixes them to match. Never redesigns.
---

# Brain polish

Brain already has a look: playful, springy, theme-aware. This skill makes every part of it follow the same rules, so a new button gets the same squish, colour and motion as the old ones. It encodes Brain's rules instead of generic design advice. When a rule and the code disagree, the code most of the app already uses wins. Ask before inventing anything new: a token, a colour meaning or a motion style.

## Brain's rules

**Colour: tokens only.** Use `--bg --tile --tile-2 --tile-3 --line --ink --muted --dim` for surfaces and text, and `--amber --mint --rose --sky --lilac` with `--amber-ink`/`--mint-ink` for accents. No hex or `rgba()` literals in UI code. Make tints with `color-mix(in srgb, var(--x) N%, transparent)`. Every colour must work in all three themes in `app/theme.css`: dark, light and Soft. JS that needs a colour (confetti) reads it with `getComputedStyle(document.documentElement).getPropertyValue("--x")`. The one exception is the graph's `CIRCLE_COLORS`.

**Colour meanings.** Amber means primary action or current. Mint means done, yes or ok. Rose means due, error or destructive. Sky and lilac are categories. Capture types: note is amber, idea lilac, todo mint, person sky, habit rose.

**Type.** `--sans` for text, and `--mono` at 500 weight and 11–12px for meta labels, dates and counts. `--display` is only for cover headlines and note titles.

**Shape.** Tiles use a 18px radius. Sheets, textareas and `.go` buttons use 14px. Inputs and banners use 12px. `.ib` icon buttons use 11px. Pills and chips use 999px.

**Every tappable thing:**
- Joins the shared transition list (`transform .18s var(--ease), background-color .2s, color .2s`).
- Has an `:active` press: `scale(.92)` for small buttons, `.94` for the fab, `.985` for full-width rows. Section headers darken their label instead.
- Shows the shared `:focus-visible` ring (2px `--amber`, offset 2; `--acc` inside capture).
- Uses `opacity: .4` when disabled.
- Has a touch target of at least about 36px tall.

**Motion.**
- `--ease` for ordinary transitions; `--spring` for playful overshoot (switches, toasts, pops, icon flips).
- WAAPI uses the `spring()` helper. Never a hand-made bounce cubic-bezier.
- Entrances use the `rise` keyframe, staggered .04–.07s.
- Confetti is only for completions (Done, Save).
- Every CSS animation or transition is also listed in the `prefers-reduced-motion` block. Every JS animation is guarded by `calm()`.

**Parity.** The phone app (`mobile/index.html`) and the desktop app (`app/index.html`) follow the same rules. A fix in one gets checked in the other.

## The pass

1. **Branch.** In `~/Notes/.brain`, run `git checkout -b polish-<date>`. Never polish on `main`.
2. **Scan.** Run these and read the hits in context:
   - `grep -nE '#[0-9a-fA-F]{3,8}\b|rgba?\(' mobile/index.html app/index.html` finds colour literals.
   - `grep -nE 'cubic-bezier\([^)]*1\.[0-9]' mobile/index.html app/index.html` finds hand-made bounces.
   - Compare every `<button` class and every `[data-*]` click target against the transition, `:active` and reduced-motion lists.
   - Find every `@keyframes` and `.animate(` and check that reduced motion covers it.
3. **List the drift** as one short table: the element, the rule it breaks, and the fix. Classify each one:
   - a local defect: fix it;
   - a one-off that should reuse a shared class: fix it;
   - a missing token or a new meaning: ask the user.
4. **Fix** everything in the first two classes in one batch. Keep the diff small and leave copy and layout alone.
5. **Look.** Render at phone size in dark and light, plus the capture sheet, with headless Chrome. It has a ~500px minimum width, so use `--window-size=500,1000`. Copy `app/theme.css` next to a copy of `mobile/`. Inject the real `~/Notes/phone/state.json` as `S` and call `render()`. Collect JS errors from `window.onerror`. WAAPI doesn't advance in headless Chrome, so force it with `getAnimations().forEach(a => a.finish())` to test animation endings. Do one round, fix, and confirm once.
6. **Report** in about five lines: what drifted, what you fixed, what needs the user's call, and screenshots. Merge to `main` (which publishes the phone app) only when the user says so.
