# AGENTS.md

## Project structure

- `AGROMARGEN_2026-27.html` - Agricultural margin calculator (4567 lines)
- `.agents/skills/` - Installed OpenCode skills
- `skills-lock.json` - Tracks installed skills
- `SKILL.md` - Main skill definition

## Main application (AGROMARGEN_2026-27.html)

Single-file SPA for agricultural economic analysis. No build system - open directly in browser.

### Features
- Margin calculation for 7 crops/sequences:
  - Soja 1°
  - Trigo + Soja 2°
  - Trigo
  - Maíz Temprano
  - Maíz Tardío
  - Sorgo
  - Girasol
- Decision panel (rentabilidad por hectare)
- Break-even analysis (punto de equilibrio)
- Quality bonifications (bonificaciones por calidad)
- Price coverage (cobertura de precios)
- Sensitivity analysis (análisis de sensibilidad)
- Charts (Chart.js + chartjs-plugin-annotation)
- PDF export (jsPDF + jsPDF-autotable)
- Excel export (SheetJS/xlsx)
- Toast notifications
- Modal dialogs
- BCRP price fetch (Banco Central de Reserva del Perú)
- Save/load scenarios (localStorage)
- Presentation mode

### Tech stack
- Vanilla HTML/CSS/JS (no framework)
- Google Fonts (Manrope)
- External CDN:
  - SheetJS (xlsx)
  - Chart.js
  - chartjs-plugin-annotation
  - jsPDF
  - jsPDF-autotable

## Installed skills

| Skill | Description |
|-------|-------------|
| `agent-browser` | Browser automation - navigate, fill forms, click, scrape |
| `brainstorming` | Creative work, feature design, requirements |
| `frontend-design` | UI creation, web components, styling |
| `skill-creator` | Create/evaluate skills, benchmarks |
| `systematic-debugging` | Bug investigation, root cause analysis |

## Development

- No build system or package.json
- Open the HTML file directly in a browser
- Edit with any text editor
- No tests or lint commands

## Workflow

- Skills loaded from `.agents/skills/`
- OpenCode auto-triggers skills based on user requests
- No special setup required
- Use the `systematic-debugging` skill for bugs
- Use the `brainstorming` skill for new features

## Pending features (design phase)

- Login with Gmail via Supabase + sync of user configurations/scenarios