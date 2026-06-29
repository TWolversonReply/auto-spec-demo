# auto-spec-demo

A reusable demonstration of how to automatically surface [Spec Kit](https://github.com/github/spec-kit) documentation via GitHub Pages during project initiation — designed for live, real-time visibility in meetings.

## What This Demonstrates

When bootstrapping a new project with Spec Kit, the documentation produced by the spec-driven workflow (constitution, specifications, plans, tasks) is typically buried in dotfiles and feature branches. This repo shows a **zero-friction pattern** for exposing that documentation as a live website that updates automatically as specs are written.

### The Pattern

1. **Initialise spec-kit** in any repo (`specify init --here --integration copilot`)
2. **MkDocs + GitHub Actions** builds and publishes only the *meaningful* documentation — not templates or internal tooling
3. **5-second auto-refresh** means meeting attendees watching the page see new content appear as it's committed
4. **Selective publishing** — only the constitution and completed feature specs are exposed; empty templates and scaffolding are excluded

### How It Works

```
Push to main
    │
    ▼
GitHub Actions workflow
    │
    ├── Assembles docs from:
    │     • .specify/memory/constitution.md  →  /constitution/
    │     • specs/**                         →  /specs/
    │
    ├── Builds with MkDocs Material
    │
    └── Deploys to GitHub Pages
            │
            ▼
    Live site (auto-refreshes every 5s)
```

### Sociotechnical Architecture Workflow

- Canonical model: `model/context-map.cml`
- Generated artifact: `images/context-map.png`
- Constitution/model changes that affect domains, ownership, or boundaries must regenerate the diagram.
- In Speckit workflows, run `/speckit.diagram-gen.update` (also configured as the mandatory `after_constitution` hook).

### Key Design Decisions

| Decision | Rationale |
|----------|-----------|
| MkDocs over Jekyll | Renders plain `.md` files without requiring front matter — compatible with spec-kit's output |
| Build-time assembly | Source files stay in their spec-kit standard locations; no duplication in the repo |
| Auto-refresh meta tag | Enables live demo without attendees manually refreshing |
| Selective inclusion | Only publishes work-in-progress/complete docs, not empty templates |
| Valorem Reply branding | Demonstrates theming capability for client-facing presentations |

## Quick Start (Reuse This Pattern)

### Prerequisites

- GitHub repo with Pages enabled (source: GitHub Actions)
- [uv](https://docs.astral.sh/uv/) for installing the Spec Kit CLI

### Steps

```bash
# 1. Install spec-kit
uv tool install specify-cli --from git+https://github.com/github/spec-kit.git@v0.9.5

# 2. Initialise in your repo
specify init --here --integration copilot --force

# 3. Copy these files from this repo into yours:
#    - mkdocs.yml
#    - overrides/main.html
#    - docs_assets/stylesheets/extra.css
#    - .github/workflows/deploy-pages.yml
#    - index.md

# 4. Switch GitHub Pages source to "GitHub Actions" in repo settings

# 5. Push — your site is live
```

### Adapting the Theme

Edit `docs_assets/stylesheets/extra.css` to match your organisation's brand colours and fonts. The `overrides/main.html` template controls the auto-refresh interval.

## Live Site

https://twolversonreply.github.io/auto-spec-demo/

