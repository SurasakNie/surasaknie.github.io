# GitHub Pages — multi-project architecture

This document describes how the three independent project sites under
`surasaknie.github.io` are wired up, why it was set up this way, and how to
add new content.

## URLs

| URL | Audience | Source repo / branch / path |
| --- | --- | --- |
| https://surasaknie.github.io/project-index/ | Owner only (private hub — don't share) | `SurasakNie/surasaknie.github.io` → `main` → `project-index/` |
| https://surasaknie.github.io/naodec/ | NaoDec team only | `SurasakNie/naodec` → `main` → repo root |
| https://surasaknie.github.io/dronekyll/ | Dronekyll team only | `SurasakNie/dronekyll` → `main` → repo root |

`/project-index/` is the only page that links to the other two. The team
sub-sites contain no link back to the hub, so giving a team their URL does
not expose the others.

> **Privacy is by URL obscurity, not authentication.** Free GitHub Pages is
> public — anyone with a URL can visit. Don't share the hub URL.

## Repository layout

```
SurasakNie/surasaknie.github.io     User Pages repo
├── main branch                     ← Pages source
│   ├── .nojekyll
│   ├── README.md
│   ├── pages-architecture.md       (this file)
│   ├── .github/workflows/update-index.yml
│   └── project-index/
│       └── index.html              (auto-generated)
├── NaoDec branch                   frozen archive
└── Dronekyll branch                frozen archive

SurasakNie/naodec                   Project Pages
└── main branch
    ├── .nojekyll
    ├── README.md
    ├── .github/workflows/update-index.yml
    ├── index.html                  (auto-generated)
    └── *.html                      (project documents)

SurasakNie/dronekyll                Project Pages
└── main branch
    ├── .nojekyll
    ├── README.md
    ├── .github/workflows/update-index.yml
    ├── index.html                  (auto-generated)
    ├── Rocket_Wireframe.html
    └── assets/hero-rocket.png
```

## Why three separate repos

GitHub User Pages (`<username>.github.io`) deploys from **exactly one
branch**. There is no built-in way to map each branch of a single repo to
its own URL subpath. Putting each project in its own repo gives each one:

- Independent deployment (each repo's Pages builds on its own pushes).
- An automatic Project Pages URL at `surasaknie.github.io/<repo-name>/`.
- Independent history and access control surface (collaborators per repo).

`SurasakNie/surasaknie.github.io` is then free to act purely as the private
hub at `/project-index/`. Project Pages URLs are shadowed by any matching
path on the User Pages site, which is why we keep `main` empty at the root
(no `/naodec/` or `/dronekyll/` folder there).

## Auto-updating index pages

Every repo has `.github/workflows/update-index.yml`. On every push to
`main`, the workflow:

1. Scans the relevant folder for `*.html` files (excluding `index.html`).
2. Regenerates an `index.html` listing each file.
3. If the regenerated file differs, commits it as
   `chore: regenerate ... index.html` under the `github-actions[bot]`
   identity.

The hub workflow (in `surasaknie.github.io`) additionally hardcodes a
"Projects" section linking to `/naodec/` and `/dronekyll/` so those entries
are always present regardless of local files.

## Adding new content

### Add a document to NaoDec or Dronekyll

1. Push a new `*.html` file to the `main` branch of `SurasakNie/naodec`
   or `SurasakNie/dronekyll`.
2. Within ~30 seconds the auto-index workflow regenerates that repo's
   `index.html` and the new file appears at
   `surasaknie.github.io/<repo>/<file>.html`.

### Add a hub document

1. Push a new `*.html` file under `project-index/` on the `main` branch
   of `SurasakNie/surasaknie.github.io`.
2. The hub workflow regenerates `project-index/index.html`.

### Filename rule

**Avoid spaces in HTML filenames.** The auto-index shell loop splits on
whitespace and produces broken links. Use underscores instead — e.g.
`Rocket_Wireframe.html`, not `Rocket Wireframe.html`. Same applies to
referenced asset paths.

## Archived branches

The original `NaoDec` and `Dronekyll` branches on `surasaknie.github.io`
are kept as frozen history. They are no longer the Pages source, so
nothing on them is published. Safe to delete later via the GitHub UI if
desired.
