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
- **TrunkText** — AI document search (blue `#6db3ff`)
- **TrunkSubmittal** — Submittal review automation (green `#4aedb5`)
- **TrunkReview** — Drawing set comparison (purple `#c0adff`)

## Layout
- **Full-width top bar**: # of Projects (NumBox), Avg Project Size (NumBox), Total Portfolio display, product toggle chips
- **Left column** ("Inputs & Assumptions"): Three product cards side-by-side, each with sliders, Advanced Assumptions toggle, and CalcBreakdown line items. Yellow accent bar on left edge.
- **Right column** ("Your ROI"): Sticky panel. Value Breakdown card (1st order, 2nd order, hours recovered, FTE equivalent, per-product chips, total annual) + Cost of Doing Nothing card (large red monthly figure + quarterly/annual sub-line). Green accent bar on left edge.

## Key architecture decisions
- **Single-file app**: Everything in one HTML file so it can be hosted on GitHub Pages without a build step.
- **DEPLOY_URL**: Set at the top of the script to `https://dayu-trunktools.github.io/roi-calculator/roi-calculator.html`. Makes the "Share / Customize" button generate correct public links. Without it, share links fall back to `window.location` (works when hosted) or show a warning (when run as a local file).
- **URL state encoding**: `#s=<base64>` hash encodes all inputs (company name, # projects, avg size, enabled products, all sliders). Anyone opening that link gets the calculator pre-populated with exactly those settings. Each customer gets their own unique link — links are independent and nothing is stored server-side.
- **PDF generation**: Pure jsPDF — no server, no SVG, everything drawn programmatically. McKinsey-style layout: dark navy header with "T" logo, yellow accent stripe, two hero metric boxes (green annual value + red monthly cost), executive summary paragraph, value-by-solution bar chart, value components table, cost of delay table, key assumptions table, paginated footer.
- **InfoTooltip**: Every input field and every results metric has a `?` tooltip explaining what it is, how it's calculated, and how it relates to other fields. Uses `ReactDOM.createPortal` to render above all other elements.

## ROI calculation logic
All calculations are per-project first, then multiplied by `numProjects`.
- **TrunkText 1st order**: field users × searches/day × working days × (current search time − TT search time, in hrs) × loaded labor cost
- **TrunkText 2nd order** (optional, toggle in Advanced): searches × % wrong info × % prevented × avg cost/incident
- **TrunkSubmittal 1st order**: submittals/yr × hrs/review × time reduction % × reviewer cost
- **TrunkSubmittal 2nd order**: avg project value × rework rate × % rework from errors × % errors caught
- **TrunkReview 1st order**: drawing sets/yr × hrs/review × time reduction % × reviewer cost
- **TrunkReview 2nd order**: drawing sets × unclouded changes/set × % caught × % with cost impact × avg cost/missed change
- **FTE equivalent**: total hours recovered ÷ 2,000 (250 days × 8 hrs)
- **Cost of Doing Nothing**: total annual value ÷ 12 (pure opportunity cost, not a fee)

## Results panel fields and their tooltips
Every field in the right "Your ROI" panel has an explanatory tooltip:
- **1st Order — Labor**: sum of hours saved × loaded cost across all products
- **2nd Order — Cost Avoidance**: rework avoidance (Submittal) + missed change cost (Review) + optional risk avoidance (Text)
- **Hours recovered**: total billable hours freed, feeds into 1st order dollar value
- **FTE equivalent**: hours ÷ 2,000
- **TrunkText chip**: full search-time formula
- **TrunkSubmittal chip**: both 1st and 2nd order components
- **TrunkReview chip**: both components + what "unclouded changes" means
- **Total Annual Value**: per-project × # of projects
- **Per project**: what drives it, how to scale
- **Cost of Doing Nothing**: ÷ 12, opportunity cost framing, how it compounds

## Input field tooltips (left panel)
Every slider and NumBox also has a `?` tooltip:
- **# of Projects**: main scaling factor
- **Avg Project Size**: affects 2nd order only (not labor)
- **Field Users / Project**: who counts, direct multiplier
- **Searches / Day / User**: industry average 2–5
- **Current Search Time**: has detailed "where the time goes" workflow breakdown
- **TrunkText Search Time**: AI search time default 1 min
- **Loaded Labor Cost**: fully loaded (salary + benefits + overhead)
- **Working Days / Year**: when to adjust from 250
- **% Searches with Wrong Info**: industry 3–8%, why small % matters at scale
- **% Prevented by TrunkText**: conservative 50–70%
- **Avg Cost per Incident**: $500–$25K+ range
- **Submittals / Year**: typical 1,000–5,000 for large commercial
- **Hours / Review**: has detailed "where the time goes" workflow breakdown
- **Time Reduction (Submittal)**: 80% = 45 min → 9 min
- **Rework Rate**: industry average 4–12% of project value
- **Errors Caught**: intentionally conservative
- **Reviewer Cost (Submittal)**: PE/PM loaded rate, $50–$120/hr
- **% Rework from Errors**: submittal-specific, 20–40% of total rework
- **Drawing Sets / Year**: typical 12–36 bulletins/yr
- **Hours / Review (Review)**: has full bulletin review workflow breakdown
- **Time Reduction (Review)**: where the hours come from
- **Unclouded Changes / Set**: most dangerous — not marked by EOR
- **Changes Caught**: core TrunkReview capability
- **Avg Cost / Missed Change**: $1K–$50K+ range
- **Reviewer Cost (Review)**: PE/VDC engineer loaded rate
- **% Changes with Cost Impact**: filters out noise, conservative 40–60%

## User preferences
- No charts/graphs — "not really compelling"
- Three product columns side-by-side in the calculator (left panel)
- Results pinned to the right as a sticky panel
- Cost of Doing Nothing box is prominent (large red number)
- PDF should look McKinsey-level professional
- Keep all CalcBreakdown line items and sliders — do not simplify
- Every field should have a `?` tooltip — the customer audience (C-suite/VPs) needs context on how numbers are derived
