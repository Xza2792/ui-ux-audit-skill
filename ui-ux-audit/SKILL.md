---
name: ui-ux-audit
description: Comprehensive UI/UX quality audit and prevention system that catches the failures AI consistently ships, including broken or shifting layouts, text overflow, inconsistent components, missing states, poor contrast, undersized touch targets, accessibility violations, performance jank, and generic "AI slop" aesthetics. Use whenever the user asks to audit, review, check, critique, fix, polish, optimize, or improve any UI, page, screen, component, screenshot, or design, or says things like "the layout looks off", "something feels wrong", "this looks AI-generated", "is this ready to ship", "weird spacing", "ugly", or "broken page". ALSO consult this skill silently BEFORE writing or editing any UI code (React, JSX, HTML, CSS, Tailwind, React Native, Expo) so these failures never ship in the first place. Supports per-project profiles that override the universal defaults; an example kids-app profile doubling as a template is included.
---

# UI/UX Audit

Catch the UI problems AI consistently introduces, and either report them with concrete fixes (audit) or avoid them while writing code (prevention).

## Why this skill exists

Almost every AI UI failure traces back to one root cause: the model generates a plausible static snapshot of a single state (usually the happy path on a desktop viewport), it cannot see the rendered result, and it carries no persistent design system across generations. The output looks right in a screenshot and breaks the moment content grows, the viewport shrinks, an error occurs, or a second component is generated with slightly different padding. A human developer carries an internalised model of "what happens when this grows, shrinks, errors, or gets tapped." This skill forces that model into view: every check below exists because AI reliably gets it wrong.

## The three modes

**1. Full audit.** Triggered by explicit requests like "run a full UI audit", "audit this app/page/component", "full design review", "is this ready to ship". This is the heavyweight, systematic pass: read ALL reference files listed below, walk every category in order, and produce the scored report defined in this file. Do not skip categories because they look fine at a glance; AI failures hide in the categories that were not checked.

**2. Quick review.** Triggered by narrower requests like "review this button", "fix the spacing here", "why does this look off". Identify which categories the request touches, read only the matching reference files, scan, and report findings in the same finding format but without the full scored ceremony (severity tags still required; score optional unless asked).

**3. Prevention.** Active whenever writing or editing any UI code. Before writing, run the Pre-flight checklist at the bottom of this file mentally, and read any reference file relevant to what is being built (e.g. building a form: read `references/color-states-forms.md`). Do not produce a report; just ship code that passes the audit it would otherwise fail.

In all modes: if a project profile applies (see Project profiles below), its rules override the universal defaults.

## Inputs

Accept any of: pasted code (React/JSX, HTML, CSS, Tailwind, React Native/Expo), files in the working directory, screenshots, or a live URL description. Rules:

- Code input: cite exact locations (file, component, offending snippet). Run the code-level checks.
- Screenshot input: run only visually verifiable checks (hierarchy, contrast, spacing rhythm, alignment, copy, aesthetic tells). Sizes and spacing are estimates, so label them as estimates against a stated reference ("assuming the nav bar is 64px, this target is roughly 32px"). State that confidence is lower than a code audit, never invent line numbers or class names, and mark anything code-dependent (states, tokens, performance, a11y wiring) as "verify in code". Screenshot audits still produce a score, marked "(provisional, screenshot-based)"; when a finding's severity depends on a measurement the screenshot cannot confirm, state the conditional escalation inside the finding ("Warning; Critical if the ratio measures below 3:1"). When projecting an escalated score, the new severity REPLACES the original deduction (a Warning escalating to Critical moves that finding's deduction from 4 to 8, never 4 plus 8). Score only what the captured state shows; risks that would only manifest at other viewports, locales, or states are listed under Verify in code and not deducted.
- If the user mentions a file that is not actually present, say so and ask for it instead of auditing from imagination.

## Severity and scoring

Use exactly four severities. Judge by user impact, not by how easy the fix is.

| Severity | Deduction | Meaning |
|---|---|---|
| **Blocker** | -12 | Stops task completion or creates legal/accessibility exposure: unreadable text, layout broken at a common viewport, keyboard/focus trap, primary action unusable or invisible, data-loss risk. |
| **Critical** | -8 | Major harm on common paths: contrast failure on body text or CTAs, missing error/empty state on a primary flow, touch targets far below floor, large layout shift on load, placeholder used as the only label. |
| **Warning** | -4 | Degrades the experience: inconsistent spacing rhythm, off-scale magic numbers, missing hover/focus polish, minor responsive squeeze, duplicate CTA intent. |
| **Polish** | -1 | Refinement: widows/orphans, tracking on caps, icon optical alignment, copy tone slips, straight quotes in body text. |

**Score = 100 minus deductions, floor 0. Always show the arithmetic**, e.g. `100 - (1x12) - (3x8) - (5x4) - (4x1) = 40/100`. The breakdown makes the score feel earned and tells the user exactly what moves the needle.

**Verdict bands:** 90-100 Ready to ship. 70-89 Ship after fixing the highest-severity findings. 40-69 Significant rework needed. Below 40 Do not ship. **Any Blocker forces "Do not ship" regardless of score.**

Calibration notes: severity scales with context. A 40px touch target is a Warning on a desktop admin tool but a Critical under a kids-app profile. A missing dark-mode token is a Polish item if the app has no dark mode. When unsure between two severities, pick the lower one and say why; inflated audits get ignored.

Default severities for the most frequent findings (override only with a stated reason):

| Finding | Default |
|---|---|
| Focus outline removed without replacement; keyboard or focus trap | Blocker |
| Horizontal scroll at 360px; layout broken at a common viewport | Critical |
| Body or CTA contrast below 4.5:1 | Critical |
| Placeholder as the only label | Critical |
| Missing empty/error state on a primary flow | Critical |
| Unsized images or embeds causing load shift | Critical |
| Touch target below 44px (below 36px, or below a profile floor on primary actions: Critical) | Warning |
| Magic-number spacing off the scale | Warning |
| Cross-component drift (radius, shadow, padding census failures) | Warning |
| Duplicate CTA intent; mixed icon families | Warning |
| Widow in a heading; straight quotes in body copy | Polish |

## Finding format

Every finding, in every mode, uses this exact shape:

```
**Found:** <what is wrong, with the offending snippet, file, or screen location>
**Why:** <who it hurts and the principle violated, one line>
**Fix:** <concrete code or change, not a vague suggestion>
**Prevent:** <lint rule, config, or automated check that stops recurrence; omit this line if none exists>
```

The **Prevent** line is what separates this audit from a one-off patch. Consult `references/durable-fixes.md` for the failure-to-tooling map. Recommend the durable fix once per failure family, not on every individual finding.

Worked findings at every severity, a model report, and a quick-review example live in `references/examples.md`. Read it before producing the first report in a session.

## What not to flag

Audit noise gets the whole report ignored. Do not flag:

- The chosen aesthetic itself. Audit execution within the direction the product chose; challenge the direction only when asked, or when it is the unjustified generic-AI default (Category 14).
- Documented intentional exceptions. A code comment or design-system rule explaining the choice ends the finding.
- Framework and library defaults that already meet the bar.
- Pure taste with no principle. Every finding names who it hurts; "I would have used more padding" is not a finding.
- More than ~3 Polish items per component unless an exhaustive polish pass was requested; fold the remainder into one summary line.
- The same root cause twice. One finding, all occurrences listed inside it.
- Established domain conventions: crypto subscript zero-count price notation, ranked lists carrying real ranking numbers, accounting negatives in parentheses, and similar. Convention is not drift.

## Full audit report template

ALWAYS use this exact structure for full audits:

```
# UI Audit: <scope>
**Score:** <arithmetic> = NN/100
**Verdict:** <band, plus "Do not ship" if any Blocker>
**Top 3 priorities:** <the three fixes that recover the most score / user impact>

## Blockers (N)
<findings>
## Critical (N)
<findings>
## Warnings (N)
<findings>
## Polish (N)
<findings>
## Verify in code (N; only when the input could not confirm them: screenshot audits, partial code)
<each item: what to check, and the severity it becomes if it fails>
## Clean
<one line listing the categories with no findings, so the user knows they were checked>
## Durable fixes
<the 1-4 tooling recommendations that would prevent the biggest failure families from returning>
```

If a severity level has no findings, omit its section. If everything is clean, say so in two sentences and still list what was checked. The Clean section may add at most two sentences naming genuine strengths that later changes must not regress; beyond that, no filler, no "great job" preamble, no restating the user's request.

### Batch audits (multiple unrelated pages or products in one request)

Score each page or product separately; never blend unrelated scopes into one average, which is meaningless. Use a condensed per-item report (score with arithmetic, verdict, findings, one strengths line) instead of the full ceremony per item, and note patterns that recur across items once, in a short cross-item summary at the end.

## The audit categories

Fifteen categories. A full audit covers all of them; a quick review covers the ones the request touches. Each reference file contains the detailed checklist, the common AI failure patterns, and fix patterns.

| # | Category | Reference file |
|---|---|---|
| 1 | Typography and text breaking | `references/structure-typography.md` |
| 2 | Layout and spacing | `references/structure-typography.md` |
| 3 | Visual hierarchy and focus | `references/structure-typography.md` |
| 4 | Color, contrast, and dark mode | `references/color-states-forms.md` |
| 5 | Component states | `references/color-states-forms.md` |
| 6 | Forms and inputs | `references/color-states-forms.md` |
| 7 | Touch and interaction | `references/interaction-responsive-a11y.md` |
| 8 | Responsive and adaptive | `references/interaction-responsive-a11y.md` |
| 9 | Accessibility | `references/interaction-responsive-a11y.md` |
| 10 | Consistency at scale and design tokens | `references/consistency-tokens-nav.md` |
| 11 | Navigation and iconography | `references/consistency-tokens-nav.md` |
| 12 | Motion and animation | `references/motion-performance.md` |
| 13 | Performance | `references/motion-performance.md` |
| 14 | Generic-AI aesthetic and content quality | `references/anti-slop-copy.md` |
| 15 | Data visualization, tables, dashboards (when scope contains data UI) | `references/data-viz-tables.md` |

Code-level sloppiness (duplicate/conflicting classes, index keys, div soup, hardcoded strings) is folded into categories 2, 10, and 14 where each item belongs.

## Audit procedure

1. **Identify scope and input type.** One component, a page, or the whole app? Code, screenshot, or both?
2. **Detect stack and project.** Tailwind or plain CSS? React, RN/Expo, or static HTML? Does a project profile apply?
3. **Read the references.** All of them for a full audit; the matching ones for a quick review. Read the project profile file if it applies.
4. **Scan systematically, category by category, in numbered order.** Do not just pattern-match the loud problems; AI failures cluster in unchecked categories. For code, actually trace what happens when content is long, lists are empty, the viewport is 360px wide, and the user is on keyboard only.
5. **Write findings as you go**, deduplicate (one finding per root cause, listing all occurrences), assign severities, compute the score, and produce the report.

## Project profiles

Profiles add stricter or different rules on top of the universal checks. Detect the project from context (file names, design tokens, the user saying so) and read the profile file when it applies; profile rules OVERRIDE the universal defaults.

- **Example: children's learning app** (touch-first, ages 4 to 9): `references/profile-example-kids-app.md`. It also serves as the template: copy it to `references/project-<name>.md`, replace the rules with the project's own, and add a bullet here with its detection cues.

When no profile applies, use the universal defaults in the reference files. If the user works repeatedly on a project with its own design system, offer once to create a profile file for it.

## Pre-flight checklist (prevention mode)

Before writing or finalizing any UI code, mentally tick every line. These are the highest-frequency AI failures; shipping code that violates one of these is shipping a known bug.

1. Text containers survive long content: `min-w-0` on flex children holding text, `break-words` where user content appears, truncation has all required pieces.
2. `text-balance` on headings, `max-w-prose` (or 65ch) on body paragraphs, no single-word last lines in headings.
3. Spacing stays on the scale (4/8px rhythm). No `p-[17px]`-style magic numbers papering over a layout problem.
4. Every interactive element has visible hover, focus-visible, and active states; focus is never removed without a replacement.
5. All states considered: loading, empty, error, disabled. Not just the happy path.
6. Touch targets at or above 44px (or the profile floor); adjacent targets have breathing room.
7. Contrast passes WCAG AA: 4.5:1 body, 3:1 large text and UI components. Check buttons and placeholder text specifically.
8. Labels above inputs, never placeholder-as-label. Errors below the field, in text, not color alone.
9. Images and embeds have explicit dimensions or aspect ratios so nothing shifts on load.
10. Animations use transform/opacity only, run 150-300ms with sensible easing, and respect `prefers-reduced-motion`.
11. One accent color, one corner-radius system, one shadow style, reused exactly; new components copy existing tokens instead of inventing values.
12. Layout verified mentally at 360px: no horizontal scroll, no two-line desktop nav, hero fits the initial viewport.
13. Semantic HTML: `<button>` for actions, `<a>` for navigation, real `<label>`s, heading levels in order.
14. No AI-default aesthetics unless the brief asks: no purple-gradient glow, no three identical feature cards, no eyebrow label above every section, no fake-precise invented numbers.
15. Copy self-audit: every visible string is grammatical, specific, and uses one register; button labels are verbs.
16. Data UI: numeric columns right-aligned with `tabular-nums`, one number-formatting rule set, charts have titled axes and units, empty-data states exist and differ from a zero reading.

If any of these is unclear or impossible given the design, surface it to the user instead of silently shipping a workaround.
