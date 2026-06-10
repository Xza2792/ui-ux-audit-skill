# Example Project Profile: Children's Learning App (Ages 4 to 9)

This file does two jobs. It is a working profile for any touch-first kids learning app, and it is the template for writing your own profiles: copy it to `references/project-<name>.md`, replace the rules with your project's own, and register it in the Project profiles section of SKILL.md with detection cues. Profile rules OVERRIDE the universal defaults in the other reference files; everything not mentioned here keeps its universal rule.

Detection (adapt to your project): file names or content referencing the app, its design tokens, kids reading/typing/learning features, or the user saying so.

Source of truth: when the repo is available, read `tailwind.config.*`, theme/token files, and any design-system docs before auditing. A profile describes the patterns; the config holds the exact values. If they conflict, the config wins and the conflict is itself a finding. Localization: if the app ships localized copy, the Category 14 localization checks apply at Warning severity minimum, using the longest target language as the stress test.

## Overrides: motor control and touch (replaces universal floors)

- **Touch targets: 56-64px minimum for primary game/learning actions, 48px floor for everything else.** A 44px target satisfies adult guidelines but not a 5-year-old's motor accuracy. Letter tiles, answer buttons, and navigation a child operates: 64px preferred. Violations here are Critical, not Warning.
- **Gaps between targets: 12-16px minimum.** Children mis-tap adjacent targets at 8px gaps.
- **Press feedback is mandatory on everything tappable.** Children re-tap when nothing visibly happens, causing double-fires. Use a clearly physical press (translate plus shadow shift, or a scale-down) and pair it with double-fire protection on game actions.
- **Game surfaces:** `touch-action: manipulation`, `select-none`, and suppressed long-press callouts so rapid or held taps never trigger zoom, selection, or context menus.

## Overrides: typography and reading (replaces universal floors)

- **Body text 18px minimum (`text-lg`); reading content 20-24px (`text-xl`/`text-2xl`); learner-facing display text larger still.** Early readers need large, well-spaced letterforms.
- **Line height `leading-relaxed` (1.625) minimum for reading content**, looser for beginner passages. Slightly increased tracking on early-reader body text aids letter discrimination.
- **Typeface rules:** rounded or geometric sans with unambiguous letterforms; check the a/g forms (single-storey preferred for beginners) and that I/l/1 are distinguishable. Strong weights (700-900) for headings and CTAs; never thin/hairline weights.
- **Reading-content casing:** lowercase-first presentation where the curriculum expects it; never style learning text in all-caps, which removes the word shapes beginners rely on.

## Overrides: color and contrast

- **Aim for 7:1 body-text contrast (WCAG AAA), not 4.5:1.** Children's contrast sensitivity and the cheap tablets they use both argue for the higher bar. 4.5:1 passes universally but is a Warning here.
- **Color never carries meaning alone, ever.** Correct/incorrect feedback always pairs color with icon + motion + (optionally) sound. Severity: Critical.

## Aesthetic enforcement (EXAMPLE: replace with your design system)

This section shows how a profile locks an aesthetic; the example system is neobrutalism-lite. Swap these rules for your own design system's, keeping the same level of specificity. Flag violations as Warning (aesthetic drift) unless they also break a universal rule.

- **Shadows: hard offsets, never blurs.** Pattern: `shadow-[4px_4px_0_0_#000]`. No `shadow-md`/`shadow-lg` Material blurs. One consistent shadow direction across the whole surface.
- **Borders: solid, 2-4px, black or near-black.** No 1px hairlines; dashed only when it communicates something.
- **Radius: modest and locked.** `rounded-md`/`rounded-lg` for surfaces, `rounded-xl` for large cards/buttons needing softness; `rounded-2xl`+ only for chips and pills; never `rounded-full` on rectangles.
- **Solid fills only.** No gradients on surfaces, no glassmorphism, no `backdrop-blur`, no semi-transparent panels over busy backgrounds.
- **Palette: bold saturated solids, one or two accents, on clean grounds.** A pastel-only or beige-only drift is the wrong aesthetic for this system, as is the AI-purple gradient look.
- **Press feedback is physical** (translate + shadow collapse), not a fade.
- **Iconography: solid or thick-stroke icons** matching the border weight; no delicate 1px line icons.

## Kids-specific conduct rules (Blockers)

- **No autoplay audio.** Sound is user-initiated or follows an explicit interaction; a global mute is reachable from every screen. Surprise audio in a kids app is a Blocker.
- **No dark patterns whatsoever:** no countdown pressure, no streak-shaming, no "are you sure you want to stop playing?" guilt prompts. Encouragement only.
- **Failure states never punish.** Wrong answers get neutral-to-warm feedback and an immediate retry path; no harsh buzzers, no red full-screen flashes.
- **Reward animations are short and skippable** (1-2s, tap to skip) so they celebrate without training impatience or hijacking the session.
- **Any external link, purchase, or settings surface sits behind a parent gate**, visually separated from the child UI.
- **Reduced-motion compliance is strict:** vestibular-triggering motion (parallax, large zooms, screen shake) is avoided entirely, not just behind the media query; gentle motion remains.

## Expo/React Native notes (if the project uses RN)

- On native screens, Tailwind web utilities translate via the project's styling layer (e.g. NativeWind); verify the press-feedback and shadow patterns have native equivalents (elevation/shadow props differ on Android/iOS).
- Safe areas via the safe-area context on every screen with bottom-anchored controls.
- Touch targets use `hitSlop` only as a patch; prefer sizing the pressable itself so the visual matches the target.
