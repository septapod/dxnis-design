# design.dxn.is: Project Status

## Current State
Level 1 (reference gallery) and Level 2 (DSL rendered page, shared nav, copy button, jsDelivr token pipeline, purge button) both shipped and live at design.dxn.is. Custom domain active. Tokens sync automatically with dxn.is via jsDelivr CDN.

## Recent Changes

### Level 2 + token sync pipeline (2026-04-11): COMPLETE

- **DSL rendered page at root.** Full style guide in the DSL's own aesthetic. Color swatches (dark theme + light theme side by side), typography specimens with metadata, component showcase (buttons with signature gold-fill hover + gradient underline sweep, inputs with focus outlines), 12-column grid visualization, spacing token reference, four elevation shadow tokens rendered on cards, two-column do's and don'ts list.
- **Gallery moved to `/gallery`.** Filter by theme, style, accent, vibe. Typography specimens now render in real or closest-substitute fonts (Satoshi from Fontshare, Switzer substituting for Sohne, Ranade substituting for Styrene B, Source Serif 4 substituting for Tiempos, Spectral substituting for Lyon, Nunito substituting for Circular, Geist real from Google Fonts, etc.).
- **Shared nav** across both pages. DSL · Gallery · Source. Gold highlighted active state.
- **jsDelivr token pipeline.** `index.html` loads `https://cdn.jsdelivr.net/gh/septapod/dxnis@main/css/dsl-tokens.css`. Any token change in the dxnis repo's main branch propagates to design.dxn.is automatically. No token duplication between the two sites.
- **Copy DESIGN.md button.** Fetches `https://raw.githubusercontent.com/septapod/dxnis/main/DESIGN.md` and puts it on the clipboard with a toast confirmation.
- **Purge cache button.** Hits `https://purge.jsdelivr.net/gh/septapod/dxnis@main/css/dsl-tokens.css` to force jsDelivr to refresh its cache instantly. Useful during active token iteration since default cache TTL is 12 hours.
- **DESIGN.md auto-sync.** `scripts/sync-design-md.mjs` in the dxnis repo regenerates the colors, spacing, and radii sections of DESIGN.md from `dsl-tokens.css`. Run with `node scripts/sync-design-md.mjs`. Tested, works.

### Level 1 (reference gallery) (2026-04-11): COMPLETE

- Initial gallery shipped with 16 curated references from VoltAgent `awesome-design-md` catalog (Notion, Claude, Linear, Vercel, Mintlify, Stripe, Apple, Airbnb, The Verge, WIRED, Framer, Supabase, Raycast, PostHog, Cal.com, Nike).
- Filter controls across four axes: theme, style, accent, vibe.
- Typography specimens added to each tile showing display and body fonts in real or closest-match Google Fonts / Fontshare alternatives.
- Deployed to Vercel. Custom domain design.dxn.is configured.

## What's Left

### Near-term

- Re-point "Copy DESIGN.md" URL from `@main` to a tagged release when the site rebuild ships (optional, stability improvement)
- Add a GitHub Action to hit the purge endpoint automatically on push to `css/dsl-tokens.css` in the dxnis repo
- Expand the reference gallery with any new references Brent finds useful

### Level 3 (aspirational, not scheduled)

- Form-based DESIGN.md generator. Pick a reference, customize colors and fonts, preview, download or push to a GitHub repo via API.
- Auth (so only Brent can push to his repos).
- Live editing of the DSL tokens from the browser with direct commit-back to `dsl-tokens.css` via GitHub API.

## Related

- `~/dev/dxnis/`: the consulting site. Shares tokens via jsDelivr.
- `~/dev/dxnis/DESIGN.md`: full design system doc (v2.1, Stitch 9-section format).
- `~/dev/dxnis/scripts/sync-design-md.mjs`: auto-regenerates DESIGN.md token sections from dsl-tokens.css.
- `~/.claude/skills/design-md/`: Claude Code skill that supports three modes: init from catalog, extract from code, extract from Stitch.
- Canonical website positioning and IA work: `proofeditor.ai/d/n4ztczvp` (Proof Editor doc).
- Framework sidebar workstream: `proofeditor.ai/d/9pdfoptp`.
