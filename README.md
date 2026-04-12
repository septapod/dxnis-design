# design.dxn.is

Design system hub for Dixon Strategic Labs. Hosted at [design.dxn.is](https://design.dxn.is).

## What it is

Two pages on one static site:

- **`/` (root): The DSL rendered as a living style guide.** Every color swatch, typography specimen, component, and layout token from `dxn.is` rendered in the DSL's own aesthetic. Functions as the public face of the Dixon Strategic Labs brand system.
- **`/gallery`: Reference gallery.** A filterable browser of reference design systems from the VoltAgent `awesome-design-md` catalog. Filter by theme, style, accent color, and vibe. Click any tile to open its full DESIGN.md at getdesign.md.

## Single source of truth for tokens

All design tokens (brand colors, theme tokens, spacing, radii, shadows, transitions, fonts) are loaded directly from the `dxnis` repo on GitHub via jsDelivr CDN.

```html
<link rel="stylesheet" href="https://cdn.jsdelivr.net/gh/septapod/dxnis@main/css/dsl-tokens.css">
```

This means:

- **No token duplication.** The CSS file is the one place changes happen.
- **Live sync with dxn.is.** Any edit to `css/dsl-tokens.css` in the `dxnis` repo propagates to both dxn.is (the live consulting site) and design.dxn.is automatically.
- **12-hour cache TTL** on `@main`. A "Purge token cache" button in the bottom-right of the DSL page hits jsDelivr's purge endpoint and gives instant updates when needed.

The only `:root` variables defined locally in `index.html` are three page-specific values (`--font-mono`, `--max-width`, `--site-gutter`). Everything else comes from the external file.

## Level status

- **Level 1:** Reference gallery with color swatches and metadata. SHIPPED.
- **Level 2:** DSL rendered page at root, shared nav, copy DESIGN.md button, jsDelivr token pipeline, purge cache button. SHIPPED.
- **Level 3 (aspirational):** Full DESIGN.md generator with form-based customization, preview, download, GitHub API integration for pushing generated files into project repos. Not started.

## Development

```bash
# Serve locally
python3 -m http.server 8000
open http://localhost:8000

# Deploy to Vercel
vercel --prod
```

## File structure

```
~/dev/dxnis-design/
├── index.html           # DSL rendered page (root)
├── gallery.html         # Reference gallery (/gallery via Vercel cleanUrls)
├── docs/
│   └── level-2-plan.md  # Historical plan doc from when Level 2 was proposed
├── vercel.json          # cleanUrls + trailingSlash config
├── README.md            # This file
└── .gitignore
```

## Related files in other repos

- `~/dev/dxnis/css/dsl-tokens.css`: source of truth for all tokens
- `~/dev/dxnis/DESIGN.md`: full design system doc (v2.1, Stitch 9-section format)
- `~/dev/dxnis/scripts/sync-design-md.mjs`: auto-syncs DESIGN.md token sections from dsl-tokens.css
- `~/.claude/skills/design-md/`: skill that uses this gallery to initialize new project DESIGN.md files

## Token update workflow

When editing DSL tokens, the order is:

```bash
# 1. Edit the CSS in dxnis repo
cd ~/dev/dxnis
vim css/dsl-tokens.css

# 2. Regenerate DESIGN.md documentation
node scripts/sync-design-md.mjs

# 3. Commit and push to main
git add css/dsl-tokens.css DESIGN.md
git commit -m "feat(tokens): <what changed>"
git push

# 4. Purge jsDelivr cache for instant update on design.dxn.is
#    Either click the "Purge token cache" button on the site,
#    or hit the purge URL directly:
curl https://purge.jsdelivr.net/gh/septapod/dxnis@main/css/dsl-tokens.css
```

Both dxn.is and design.dxn.is now reflect the new tokens.

## Expanding the reference gallery

Sites in `/gallery` are defined in the `SITES` array inside `index.html` (embedded JS, no build step). The upstream curated catalog lives at `~/.claude/skills/design-md/catalog.json`. To add a reference, update both files with matching entries.

## License

MIT. Gallery data sourced from the public VoltAgent `awesome-design-md` repo.
