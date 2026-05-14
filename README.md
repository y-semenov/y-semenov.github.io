# Yaroslav Semenov — Engineering Portfolio Website

Personal portfolio site for **Yaroslav Semenov**, a mechanical / automotive engineer.
The site is linked from job applications in Germany alongside his CV, so its job is to
present engineering work clearly and credibly to recruiters and hiring engineers.

- **Live site:** https://y-semenov.com
- **Repository:** https://github.com/y-semenov/y-semenov.github.io (public)

> **Why this file exists:** it gives an AI assistant (or any new contributor) the full
> context of the project up front — what it is, how it's built and deployed, and the
> conventions to follow — so none of that has to be re-explained each session.

---

## Tech stack

- **Plain static site** — hand-written HTML, one shared CSS file, and small inline
  vanilla JS. There is **no build step, no framework, no package manager, no
  dependencies.** Edit a file, refresh the browser — that's the whole dev loop.
- **Fonts** — `Inter` is loaded from Google Fonts on `index.html` only (it is used just
  for the hero-video subtitle overlay). Every other page and element uses a
  `Segoe UI` / system-font stack defined in `style.css`.
- **Analytics** — Google Tag Manager (`GTM-KRX925MB`), embedded near the top of
  every page plus the `<noscript>` fallback right after `<body>`.

## Local development

Open `index.html` in a browser, or — better — serve the folder, because the project
pages use root-absolute links (`/`, `/#projects`) and the Open Graph tags use absolute
URLs:

```
python -m http.server 8000
# open http://localhost:8000
```

## Deployment

- Hosted on **GitHub Pages**, deployed by **GitHub Actions** — see
  [.github/workflows/static.yml](.github/workflows/static.yml).
- The workflow runs on every push to **`main`** (and on manual dispatch). It uploads the
  **entire repository** (`path: '.'`) as the Pages artifact, so *anything* committed to
  `main` — including the `info/` folder — becomes publicly reachable on the live site.
- **Custom domain:** `y-semenov.com`, used as the canonical host in every page's Open
  Graph tags. The Pages custom-domain / `CNAME` configuration lives outside the current
  branch — verify on `main` before touching anything domain-related.
- Work is done on **feature branches**; changes only go live once merged to `main`.

## Repository structure

```
/
├── index.html                # Landing page (hero, projects, skills, education, contact)
├── style.css                 # Single shared stylesheet for ALL pages
├── .github/workflows/
│   └── static.yml            # GitHub Pages deploy workflow
├── projects/
│   ├── ktl/index.html        # MAHLE — FEM simulation of KTL coating
│   ├── bus/index.html        # MAHLE — Bus A/C prototype validation (bachelor's thesis)
│   ├── nvh/index.html        # BMW — NVH test bench for refrigerant hoses
│   ├── battery/index.html    # BMW — Li-Ion battery safety (master's thesis)
│   └── roboter/index.html    # Self-balancing robot (currently hidden — see Gotchas)
├── media/
│   ├── hero/                 # Hero pitch video, poster image, DE/EN .vtt captions
│   ├── logos/                # TUM / BMW / MAHLE logos (overlaid on the hero video)
│   ├── og/                   # Open Graph preview images — one per page
│   ├── projects/<name>/      # Per-project images and videos
│   └── ui/                   # UI assets (sound on/off icons, portrait)
└── info/
    └── 20260305 Розширений Життєпис.pdf   # Extended CV — see "About Yaroslav" below
```

## Page anatomy

### Landing page — `index.html`

Single-page layout with anchor-linked sections:

- `nav.navbar` — sticky top bar (logo + Projects / Skills / Education / Contact)
- `section.hero` — the hero **pitch video** plus name/tagline and CTA buttons
- `section#projects` — `.projects-grid` of `.project-card` links to each project page
- `section#skills` — `.skills-grid` of grouped skill lists
- `section#education` — `.timeline` (TU Munich M.Sc., RUB B.Sc.)
- `section#contact` — email, LinkedIn, phone

**Hero video system** (a non-trivial block of inline JS in `index.html`): a custom
subtitle overlay rendered from the DE/EN `.vtt` tracks with a DE / EN / Off switcher;
a HUD mute button; the university logos overlaid and JS-positioned on top of the video
between specific timestamps; and an `IntersectionObserver` that pauses/plays the video
on scroll. The logo positions (`LOGO_POS`) are hardcoded fractions tuned to the current
`media/hero/pitch.webm` — they must be re-tuned if that video is replaced.

### Project detail pages — `projects/<name>/index.html`

Every project page follows the **same template**. To add a project, copy an existing
page (`projects/ktl/index.html` is the cleanest reference) and keep this structure:

```
<head>
  - Google Tag Manager script
  - <title>Page Name – Yaroslav Semenov</title>
  - <meta name="description">
  - Open Graph tags (og:type=article, og:url, og:title, og:description,
    og:image, og:site_name, og:locale=en_GB)
  - Twitter Card tags
  - <link rel="stylesheet" href="../../style.css">
<body>
  - GTM <noscript>
  - nav.project-detail-nav     → "YS" logo to /, "← All Projects" to /#projects
  - header.project-header      → .project-tag, <h1>, .project-subtitle
  - div.project-hero           → an <img> or <video> + .caption
  - main.project-content       → <section> + <figure> blocks (the body)
      typical flow: Background → The Problem → Approach/Methodology → Results
  - section "Tools & Skills"   → .tags with one <span> per tool
  - section "See next"         → .see-next-card linking to the next project
  - footer.footer
  - optional <script>          → video hover-controls / tab switching
```

Reusable figure layouts available in `style.css`: `.figure-two-col`, `.figure-four-col`,
and `.tab-figure` (a tabbed image switcher — see `projects/nvh/index.html`).

## Conventions

- **Project URLs are folder-based:** `projects/<name>/` served as `index.html`. Use
  lowercase, hyphen-free short names.
- **Media** lives under `media/projects/<name>/`, files named in
  `lowercase-with-hyphens`. Each page also has a matching social preview image at
  `media/og/<name>.jpg`.
- **CSS is one file.** `style.css` is organised into commented sections
  (`RESET & BASE`, `NAVBAR`, `HERO`, `PROJECT CARDS`, `PROJECT DETAIL PAGES`,
  `RESPONSIVE`, …). Responsive breakpoints are at **900px** and **600px**. Add new
  styles to the matching section rather than starting a new stylesheet.
- **The GTM snippet** (head script + `<noscript>`) is duplicated on every page — keep it
  in sync if it ever changes.
- **Comments in the code** are a mix of Ukrainian and English; both are fine.

## Content & voice

- **Language:** English, `en_GB` locale. First person, concise, results-oriented.
- Project pages lead with **concrete outcomes and numbers** (e.g. "8,500 Hz first
  resonance — 3× better", "100+ sensors", "500+ experiments", "23% higher deposition
  efficiency"). Keep that style — claims should be specific and verifiable against the
  CV / thesis material.
- Structure each project as a short narrative: context → problem → what Yaroslav did →
  measurable result → tools used.

## About Yaroslav (brief)

Mechanical / automotive engineer based in Munich, open to relocation, authorised to work
in Germany; fluent in German, English and Ukrainian. Currently looking for engineering
positions in Germany.

- **M.Sc. Automotive Engineering** — Technische Universität München (2022–2025)
- **B.Sc. Mechanical Engineering** — Ruhr-Universität Bochum (2017–2022)
- Hands-on experience at **MAHLE** (internships + bachelor's thesis) and **BMW**
  (working student + master's thesis).
- Focus areas: simulation (FEM / CFD), NVH & structural dynamics, thermal management,
  battery safety, CAD.

**Full background:** [`info/20260305 Розширений Життєпис.pdf`](info/20260305%20Розширений%20Життєпис.pdf)
is an extended CV (in Ukrainian) covering every project, role, course and grade in
detail. It is the **source of truth** for the facts behind each project page — consult
it before writing or changing project copy. Note: this PDF is committed to a public
repo and is therefore live at `y-semenov.com/info/...` (see Gotchas).

## Known issues & gotchas

- **`projects/roboter/` is hidden.** Its card in `index.html` is commented out, and both
  that commented-out link and the roboter page's own "See next" card point to
  `roboter.html` instead of the folder-based `roboter/`. Its `og:url` is also
  `.../roboter.html`. Fix all three paths if the project is re-enabled.
- **`projects/battery/index.html` carries a page-specific `<style>` block** in its
  `<head>` (stat strips, callouts, factor grid, sim-step table, result highlight) — the
  only page that deviates from "style.css only". If any of those components get reused,
  consider promoting them into `style.css`.
- **The `info/` CV PDF is publicly deployed.** Because the workflow ships the whole repo,
  the extended CV — which includes personal data such as date of birth and full grade
  transcripts — is reachable at `y-semenov.com/info/...`. Confirm this is intended; if
  not, move it out of the repo (or out of `main`).
- **`media/og/ktl-old.jpg`** appears to be a leftover Open Graph image and is unused.
- **`.placeholder-box` styles** remain in `style.css` from the pre-media scaffolding;
  unused but harmless.
- **Hero video logo positions** are hardcoded to the current `pitch.webm` (see Page
  anatomy) — replacing the video means re-tuning `LOGO_POS` and the timestamp window.
