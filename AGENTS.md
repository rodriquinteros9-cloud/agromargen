# AGENTS.md

## Project structure

```
Margenes/
 ├── .agents/skills/           # OpenCode skills (9 installed)
├── AGROMARGEN_2026-27.html   # Main application (244KB)
├── AGENTS.md                # This file
├── README.md                # Project documentation
└── skills-lock.json         # Installed skills tracking
```

## Main application (AGROMARGEN_2026-27.html)

Single-file SPA for agricultural economic analysis. No build system - open directly in browser.

### Features
- **7 Crops/sequences:**
  - Soja 1°
  - Trigo + Soja 2°
  - Trigo
  - Maíz Temprano
  - Maíz Tardío
  - Sorgo
  - Girasol
- **Analysis tools:**
  - Decision panel (rentabilidad por hectare)
  - Break-even analysis (punto de equilibrio)
  - Quality bonifications (bonificaciones por calidad)
  - Price coverage (cobertura de precios)
  - Sensitivity analysis (análisis de sensibilidad)
- **Exports:**
  - PDF export (jsPDF + jsPDF-autotable)
  - Excel export (SheetJS/xlsx)
- **UI/UX:**
  - Charts (Chart.js + chartjs-plugin-annotation)
  - Toast notifications
  - Modal dialogs
  - Presentation mode
- **Data:**
  - BCRP price fetch (Banco Central de Reserva del Perú)
  - Save/load scenarios (localStorage)

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

| Skill | Description | Source |
|-------|-------------|--------|
| `agent-browser` | Browser automation | vercel-labs/agent-browser |
| `brainstorming` | Creative work, feature design | obra/superpowers |
| `frontend-design` | UI creation | anthropics/skills |
| `skill-creator` | Create/evaluate skills | anthropics/skills |
| `systematic-debugging` | Bug investigation | obra/superpowers |
| `supabase` | Supabase integration tasks | vercel-labs/supabase |
| `supabase-postgres-best-practices` | Postgres optimization best practices | vercel-labs/supabase-postgres-best-practices |
| `context7-mcp` | Library/API references and code examples | context7 MCP |
| `find-skills` | Discover/install agent skills | anthropics/skills |

### Skills structure

- **brainstorming/**
  - SKILL.md
  - visual-companion.md
  - spec-document-reviewer-prompt.md
  - scripts/

- **skill-creator/**
  - SKILL.md
  - LICENSE.txt
  - agents/
  - assets/
  - eval-viewer/
  - references/
  - scripts/

- **agent-browser/**
  - SKILL.md
- **frontend-design/**
  - SKILL.md
- **context7-mcp/**
  - SKILL.md
- **find-skills/**
  - SKILL.md
- **supabase/**
  - SKILL.md
- **supabase-postgres-best-practices/**
  - SKILL.md

- **systematic-debugging/**
  - SKILL.md
  - CREATION-LOG.md
  - test-pressure-*.md
  - condition-based-waiting*.ts

## Development

- No build system or package.json
- Open the HTML file directly in a browser
- Edit with any text editor
- No tests or lint commands

## Workflow

- Skills loaded from `.agents/skills/`
- OpenCode auto-triggers skills based on user requests
- No special setup required
- Use `systematic-debugging` skill for bugs
- Use `brainstorming` skill for new features

## Pending features (design phase)

- Login with Gmail via Supabase + sync of user configurations/scenarios
