# AGENTS.md

## Project structure

- `AGROMARGEN_2026-27.html` - Agricultural margin calculator (4567 lines)
- `.agents/skills/` - Installed OpenCode skills (see `skills-lock.json`)
- `skills-lock.json` - Tracks installed skills

## Main application (AGROMARGEN_2026-27.html)

Single-file SPA for agricultural economic analysis. No build system - open directly in browser.

**Features:**
- Margin calculation for 7 crops/sequences (Soja 1°, Trigo+Soja 2°, Trigo, Maíz Temprano, Maíz Tardío, Sorgo, Girasol)
- Decision panel, Break-even analysis, Quality bonifications, Price coverage, Sensitivity analysis
- Charts (Chart.js + chartjs-plugin-annotation)
- PDF export (jsPDF + jsPDF-autotable), Excel export (SheetJS)
- Toast notifications, modal dialogs
- BCRP price fetch
- Save/load scenarios
- Presentation mode

**Tech stack:**
- Vanilla HTML/CSS/JS (no framework)
- Google Fonts (Manrope)
- External CDN: SheetJS, Chart.js, chartjs-plugin-annotation, jsPDF, jsPDF-autotable

## Installed skills

- `agent-browser` - Browser automation
- `brainstorming` - Creative work and feature design
- `frontend-design` - UI creation
- `skill-creator` - Create/evaluate skills
- `systematic-debugging` - Bug investigation and fixes

## Development

- No build system or package.json
- Open the HTML file directly in a browser
- Edits: use any text editor
- No tests or lint commands

## Workflow

- Skills are loaded from `.agents/skills/`
- Run OpenCode normally - skills auto-trigger based on user requests
- No special setup required

## Pending features (in design phase)

- Login with Gmail via Supabase + sync of user configurations/scenarios