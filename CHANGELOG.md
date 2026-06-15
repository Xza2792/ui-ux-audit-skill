# Changelog

## 1.1.0 (2026-06-15)

- New **simulate-the-user-first** method (`references/user-simulation.md`): walk the flow as the target user, a constrained user, and an edge-case-data user before the category sweep — catches experiential failures a static scan misses (contradictory states, mislabelled scope, happy-path-only flows). Wired into the full-audit procedure.
- Three new **bundled category profiles**: SaaS / dashboard, marketing / landing, and e-commerce. The skill now switches profile by product type, with the universal defaults as the fallback.
- **Layout:** added the "fixed-width sibling starves a text column" rule (the fix is width, not `break-words`) to the typography/layout reference.
- Profiles standardised to a `profile-*.md` naming convention; profile section and README updated to list all bundled profiles.

## 1.0.0 (2026-06-10)

Initial public release.

- 15 audit categories across 10 reference files
- Three modes: full scored audit, quick review, silent prevention during UI coding
- Severity rubric (Blocker/Critical/Warning/Polish) with 0-100 scoring and verdict bands
- Durable-fix tooling map (lint rules and automated tests per failure family)
- Worked examples anchoring the finding format and report shape
- Example kids-app project profile doubling as a profile template
