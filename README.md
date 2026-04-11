# design.dxn.is

Design system hub for Dixon Strategic Labs. Hosted at https://design.dxn.is.

## What it is

A filterable gallery of reference design systems from the VoltAgent `awesome-design-md` catalog. Browse by theme, style, accent color family, and vibe. Click any tile to open the full DESIGN.md at getdesign.md.

Used as a starting point when initializing a new project's design system via the `/design-md` Claude Code skill.

## Level 1 (current)

Static HTML gallery. No build step. Deploy via Vercel.

## Level 2 (planned)

Adds a live render of the DSL (Dixon Strategic Labs) design system itself: color swatches, typographic specimens, component examples, copy-to-clipboard DESIGN.md. See `docs/level-2-plan.md`.

## Level 3 (aspirational)

Full DESIGN.md generator with form-based customization, preview, download, and GitHub API integration for pushing generated DESIGN.md files directly into project repos.

## Development

```bash
# Serve locally
python3 -m http.server 8000
open http://localhost:8000

# Deploy
vercel --prod
```

## Source

Data embedded in `index.html` as a JS array. Upstream catalog lives at `~/.claude/skills/design-md/catalog.json`. To expand the gallery, update both files.
