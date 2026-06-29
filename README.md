# auto-spec-demo

A demonstration of [Spec Kit](https://github.com/github/spec-kit) for spec-driven development, showcasing the Contoso University integration migration project.

## What This Repo Contains

### Project Constitution

A fully populated constitution (`v1.0.0`) defining the principles and governance for migrating Contoso University's integration platform from **SoftwareAG WebMethods** to **Azure Integration Services**.

Key principles established:

- Cloud-First Migration
- Domain-Driven Integration Boundaries (Students, Staff, Payments, Courses, Awards, Apprenticeships)
- Incremental Migration (domain-by-domain, not big-bang)
- Cost-Driven Decision Making (eliminating Oracle licences and on-prem overhead)
- Team Accessibility (self-service for varying technical capabilities)
- Observable by Default
- Infrastructure as Code

### Spec Kit Integration

Initialised with `specify-cli` v0.9.5 using the **Copilot** integration. Includes:

- `.github/agents/` — Copilot agent definitions for spec-kit workflow commands
- `.github/prompts/` — Prompt files for each spec-kit command
- `.specify/` — Spec Kit configuration, templates, scripts, and extensions

### GitHub Pages (MkDocs)

The repo publishes documentation to GitHub Pages using **MkDocs Material** with a custom build workflow:

- **URL**: https://twolversonreply.github.io/auto-spec-demo/
- **Auto-refresh**: Pages include a 5-second meta refresh tag
- **Branding**: Styled to match [Valorem Reply](https://www.valoremreply.com/) (Poppins font, navy header, blue accents)
- **Selective content**: Only publishes the constitution and completed feature specs — not templates or internal tooling

The GitHub Actions workflow assembles docs at build time from:

| Source | Published path |
|--------|---------------|
| `.specify/memory/constitution.md` | `/constitution/` |
| `specs/` (feature docs) | `/specs/` |

## Getting Started

### Prerequisites

- [uv](https://docs.astral.sh/uv/) (Python package manager)
- Git

### Install Spec Kit CLI

```bash
uv tool install specify-cli --from git+https://github.com/github/spec-kit.git@v0.9.5
```

### Spec-Kit Workflow

Use the slash commands with your Copilot agent:

1. `/speckit.constitution` — Establish or amend project principles
2. `/speckit.specify` — Create a feature specification
3. `/speckit.plan` — Create an implementation plan
4. `/speckit.tasks` — Generate actionable tasks
5. `/speckit.implement` — Execute implementation

Feature documentation is written to `specs/<branch-name>/` and automatically published to GitHub Pages on push to `main`.
