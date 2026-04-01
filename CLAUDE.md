# Trunk Tools ROI Calculator

## What this is
A standalone, client-facing ROI calculator for Trunk Tools. Built as a single HTML file (no build step, no dependencies to install). Target audience: C-suite and VPs at construction companies. Used by sales to share pre-configured ROI models with prospects.

## The file
- `roi-calculator.html` — the entire app. React 18 (UMD), Babel standalone, jsPDF 2.5.1, all loaded from CDN.

## Live URL
```
https://dayu-trunktools.github.io/roi-calculator/roi-calculator.html
```

## GitHub repo
```
https://github.com/dayu-trunktools/roi-calculator
```

## How to deploy updates
```bash
cd "/Users/dayu/Claude/ROI Calc"
git add roi-calculator.html
git commit -m "describe what changed"
git push
```
GitHub Pages auto-deploys within ~30 seconds of a push.

## Products covered
- **TrunkText** — AI document search (blue)
- **TrunkSubmittal** — Submittal review automation (green)
- **TrunkReview** — Drawing set comparison (purple)

## Key architecture decisions
- **Single-file app**: Everything in one HTML file so it can be hosted on GitHub Pages without a build step.
- **DEPLOY_URL**: Set at the top of the script to the GitHub Pages URL. This makes the "Share / Customize" button generate correct public links. Without it, share links would use `file://` paths when testing locally.
- **URL state encoding**: `#s=<base64>` hash encodes all inputs (company name, # projects, avg size, enabled products, all sliders). Anyone opening that link gets the calculator pre-populated.
- **PDF generation**: Pure jsPDF — no server, no SVG, everything drawn programmatically with `rect()`, `roundedRect()`, `text()`. McKinsey-style: dark navy header, hero metric boxes, bar charts, executive summary, assumptions table.
- **Two-column layout**: Left = Inputs & Assumptions (product cards with sliders + CalcBreakdown), Right = sticky Your ROI panel (value breakdown + Cost of Doing Nothing).

## ROI calculation logic
All calculations are per-project first, then multiplied by `numProjects`.
- **TrunkText 1st order**: field users × searches/day × working days × (current - TT search time in hrs) × labor cost
- **TrunkText 2nd order** (optional): searches × % wrong info × % prevented × avg cost/incident
- **TrunkSubmittal 1st order**: submittals × hrs/review × time reduction % × reviewer cost
- **TrunkSubmittal 2nd order**: avg project value × rework rate × % from errors × % errors caught
- **TrunkReview 1st order**: drawing sets × hrs/review × time reduction % × reviewer cost
- **TrunkReview 2nd order**: drawing sets × unclouded changes/set × % caught × % with cost impact × avg cost/missed change

## User preferences
- No charts/graphs — "not really compelling"
- Three product columns side-by-side in the calculator (left panel)
- Results pinned to the right as a sticky panel
- Cost of Doing Nothing box is prominent (large red number)
- PDF should look McKinsey-level professional
- Keep all CalcBreakdown line items and sliders — do not simplify
