# ui-ux-audit

A Claude skill that audits any web or mobile UI for the failures AI consistently ships, and silently prevents them while Claude writes UI code.

Almost every AI UI failure traces back to one root cause: the model generates a plausible static snapshot of a single state (the happy path, on desktop), it cannot see the rendered result, and it carries no persistent design system across generations. The output looks right in a screenshot and breaks the moment content grows, the viewport shrinks, an error occurs, or a second component is generated with slightly different padding. This skill forces all of that into view.

## What it does

Three modes, chosen automatically from how you ask:

1. **Full audit.** Say "run a full UI audit" and Claude first simulates the real user walking the flow (target user, a constrained user, an edge-case-data user), then walks all 15 categories systematically and produces a scored report: 0-100 with the arithmetic shown, a ship verdict, findings grouped by severity (Blocker -12, Critical -8, Warning -4, Polish -1), top 3 priorities, and durable-fix recommendations (the lint rule or automated test that stops each failure family from returning).
2. **Quick review.** Say "review this component" or "why does this look off" and Claude scans only the relevant categories and reports findings without the full ceremony.
3. **Prevention.** Whenever Claude writes or edits UI code, it silently runs a pre-flight checklist of the highest-frequency AI failures, so the bugs never ship in the first place.

The 15 categories: typography and text breaking, layout and spacing, visual hierarchy, color/contrast/dark mode, component states, forms, touch and interaction, responsive, accessibility (WCAG 2.2), consistency at scale and design tokens, navigation and iconography, motion, performance (Core Web Vitals), generic-AI aesthetic and copy quality, and data visualization/tables/dashboards.

Every finding follows one format:

```
**Found:** what is wrong, with the offending snippet or location
**Why:** who it hurts and the principle violated
**Fix:** concrete code or change
**Prevent:** the lint rule or test that stops recurrence (when one exists)
```

## Install

### Claude.ai (web, desktop, mobile)

1. Download `ui-ux-audit.skill` from this repository (or from Releases).
2. In Claude, open Settings, find Skills, and upload the file.
3. If you do not see a Skills section, check availability for your plan at https://support.claude.com

### Claude Code / Claude Desktop

Copy the `ui-ux-audit/` folder into your skills directory:

```bash
# personal (available in all projects)
mkdir -p ~/.claude/skills && cp -r ui-ux-audit ~/.claude/skills/

# or project-scoped (committed with the repo)
mkdir -p .claude/skills && cp -r ui-ux-audit .claude/skills/
```

Run `/skills` in Claude Code to confirm it loaded. Docs: https://code.claude.com/docs/en/skills

The skill follows the open SKILL.md Agent Skills standard, so it also works in other coding agents that support the format.

## Use

- "Run a full UI audit" on pasted code, project files, or screenshots
- "Review this component" / "fix the spacing" / "is this ready to ship"
- Or just let it work: it guides Claude automatically during any UI coding

Screenshot-only input is supported with reduced confidence (visual checks only, sizes as labeled estimates).

## Customize: project profiles

Profiles override the universal defaults for a particular *kind* of product — stricter touch targets, a design system's rules, domain conduct rules. Four ship in the box: an example children's-learning-app profile (which also doubles as the template), plus SaaS/dashboard, marketing/landing, and e-commerce. The skill detects which fits from context and loads it; with none, it uses the universal defaults. To add your own, copy `references/profile-example-kids-app.md` to `references/profile-<name>.md`, replace the rules, and register it in the Project profiles section of `SKILL.md` with detection cues.

## Structure

```
ui-ux-audit/
├── SKILL.md                                  core: modes, severity rubric, scoring, report template, pre-flight
└── references/
    ├── user-simulation.md                    simulate-the-user-first method (run before the sweep)
    ├── structure-typography.md               categories 1-3
    ├── color-states-forms.md                 categories 4-6
    ├── interaction-responsive-a11y.md        categories 7-9
    ├── consistency-tokens-nav.md             categories 10-11 + flow checks
    ├── motion-performance.md                 categories 12-13
    ├── anti-slop-copy.md                     category 14 + localization + dark patterns
    ├── data-viz-tables.md                    category 15
    ├── durable-fixes.md                      failure-to-tooling map
    ├── examples.md                           worked findings and a model report
    ├── profile-example-kids-app.md           example kids-app profile / template
    ├── profile-saas-dashboard.md             profile: data-dense SaaS / dashboards
    ├── profile-marketing-landing.md          profile: marketing / landing pages
    └── profile-ecommerce.md                  profile: e-commerce / checkout
```

The core file stays small by design: it loads on every trigger, while reference files load only when an audit runs (progressive disclosure).

## Credits

Built from research across the Claude skills ecosystem. Patterns informed by Anthropic's skill-creator and frontend-design skills, Leonxlnx/taste-skill, and community design-audit skills. Standards referenced: WCAG 2.2, Apple HIG, Core Web Vitals.

## License

MIT
