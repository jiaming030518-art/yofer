# YOFER Product Showcase

Static bilingual-ready product showcase website for YOFER automotive body kits.

## Open locally

Open `outputs/index.html` in a browser.

## Project contents

- `outputs/index.html` — website HTML, CSS, and client-side interactions
- `outputs/assets/` — product imagery, QR codes, portrait, and map assets

The site includes a homepage, a brand-filtered product catalog, shared model detail and before/after comparison views, export-market maps, and About Me, Company, and Contact pages. Model content varies with the assets available. Brands, models, styles, and market mappings are defined in `outputs/index.html`.

## Project handoff

- [AGENTS.md](AGENTS.md) — confirmed collaboration rules, visual conventions, preservation requirements, checks, and Git workflow.
- [PROJECT_HANDOFF.md](PROJECT_HANDOFF.md) — current architecture, routes, content status, historical checks, known limitations, and deployment configuration (Chinese; reviewed November 17, 2026).

Read both before continuing work and confirm the current source and Git state. Historical test results do not replace verification of new changes.

## HTTP preview

From the project root, run:

```powershell
python -m http.server 4173 --bind 127.0.0.1 --directory outputs
```

Open `http://127.0.0.1:4173/#home`. No npm build step is required for the current static site.

## Publishing

The GitHub Pages workflow in `.github/workflows/pages.yml` publishes `outputs/` on pushes to `main` or manual dispatch. Local commits do not update the public site; push and deployment are separate operations and require an explicit publishing request.
