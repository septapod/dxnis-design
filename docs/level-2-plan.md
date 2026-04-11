# design.dxn.is: Level 2 Plan

**Status:** Planned. Not yet built.
**Estimated build time:** 60-90 minutes.
**Depends on:** Level 1 shipped and live at design.dxn.is.

## Goal

Turn design.dxn.is from "catalog browser" into "the public face of the DSL brand system." The gallery becomes one tab. A new DSL page becomes the main tab. Visitors (including you, hired help, and agents) can see exactly what the Dixon Strategic Labs brand looks like rendered, and copy the DESIGN.md file with one click.

## Why this level matters

Right now, the DSL design system lives in a markdown file on Brent's laptop at `~/dev/dxnis/DESIGN.md`. Anyone working on a project has to find it, open it, and mentally render what "Near-black warm bg #09090B with gold #FBE248 accent" actually looks like. Level 2 removes that friction. The brand becomes a URL you can send anyone.

## What Level 2 adds

### New page: the DSL rendered

A page at `design.dxn.is/dsl` (or the root, with the gallery moved to `/gallery`) that shows the DSL design system as a live, visual style tile. Not a list of tokens. An actual rendered example of every token in use.

Sections on the page:

1. **Hero strip.** The brand at a glance. Gold, coral, teal, and both background shades as large swatches with hex codes. Satoshi display text, Karla body text, Lora pull quote. A signature button with the full hover animation active.

2. **Color system.** Dark theme and light theme side by side. Every surface token, every text token, every border token shown as a labeled swatch with hex code and functional role.

3. **Typography specimens.** h1, h2 (default), h2 (section label uppercase), h3, body paragraph (at 70ch max width), nav link, button text, pull quote. Each rendered at real size with font name and weight labeled.

4. **Component gallery.** Buttons (default, primary, hover state frozen, focus state frozen), cards (default, hover), form inputs (default, focus, disabled), section dividers, the signature gold gradient underline sweep.

5. **Layout demo.** A small 12-column grid visualization with column numbers, gutter sizes labeled, max-width bounded. Shows the 8px baseline grid as a subtle overlay.

6. **Elevation demo.** Shadow tokens rendered on cards at actual elevation, side by side, labeled.

7. **Do's and don'ts.** Pulled directly from `~/dev/dxnis/DESIGN.md` section 7. Rendered as a two-column list.

8. **Copy DESIGN.md button.** A sticky top-right button that copies the entire `~/dev/dxnis/DESIGN.md` file contents to the clipboard. Click, toast confirms, paste into any new project.

### Gallery page moved

The current gallery at `/` moves to `/gallery`. Root becomes the DSL rendered page. Both are linked in a small nav at the top.

### Nav

Top-right nav with three links:
1. **DSL** (the home page, rendered brand)
2. **Gallery** (the VoltAgent catalog browser)
3. **Source** (a link to the GitHub repo for the site)

## Technical approach

Two options:

### Option A: Static with hardcoded tokens

Hand-write the DSL page using the exact tokens from `~/dev/dxnis/DESIGN.md` v2.1. Faster to build (maybe 45 minutes). Risk: drifts from the source of truth unless Brent updates both files together.

### Option B: Build-time fetch from the dxnis repo

Vercel build step fetches the raw DESIGN.md from the dxnis GitHub repo, parses it (or just includes it as a string for the copy button), and injects token values into a template. Slower to build (maybe 90 minutes). Upside: always in sync. Downside: requires a build step and the tokens have to be in a machine-readable place in the DESIGN.md (either a code block or a JSON sidecar file).

**Recommendation:** Option A for the first ship. Hand-write the DSL page from the v2.1 tokens. Accept the drift risk for now. If the drift becomes a problem, upgrade to Option B later.

## File structure after Level 2

```
~/dev/dxnis-design/
├── index.html           ← DSL rendered page (new root)
├── gallery.html         ← The VoltAgent gallery (moved from index)
├── assets/
│   ├── styles.css       ← Shared styles across both pages
│   └── nav.js           ← Shared top nav
├── docs/
│   └── level-2-plan.md  ← This file
├── vercel.json
├── README.md
└── .gitignore
```

## Out of scope for Level 2

- Editing the DSL from the browser (Level 3)
- GitHub API integration for generating new DESIGN.md files (Level 3)
- Authentication (Level 3)
- Any backend at all (Level 3)
- Live preview of the DSL as you tweak tokens (Level 3)

## When to build it

Not now. Level 1 needs to ship and be live for a day or two first, so Brent can decide whether the idea holds up once it's real. After that, Level 2 is a 60-90 minute build in a focused session.

## Open questions for Level 2

1. Should the DSL page have a theme toggle (dark/light) like the main dxn.is site, or show both themes side by side in a single view? Side by side is more informative but harder to lay out. Toggle matches the main site's pattern.
2. Should the "Copy DESIGN.md" button copy the raw markdown or a pretty-printed version? Raw markdown is more useful for agents. Pretty-printed is more useful for humans reading it.
3. Should the Gallery and DSL pages share a nav, or should they feel like two distinct destinations? Shared nav is more cohesive. Distinct pages emphasizes the two different purposes.
