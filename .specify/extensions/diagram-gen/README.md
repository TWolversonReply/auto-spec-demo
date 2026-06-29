# Sociotechnical Diagram Generator Extension

This extension generates a **sociotechnical architecture diagram** that maps organisational teams to DDD bounded contexts, derived from the project constitution.

## How It Works

1. Reads the constitution at `.specify/memory/constitution.md`
2. Generates (or updates) a Context Mapper CML model at `model/context-map.cml`
3. The CML model defines bounded contexts for each business domain and maps teams to those contexts
4. The GitHub Actions build pipeline converts the CML to PlantUML and renders diagrams for the MkDocs site

## Commands

| Command | Description |
|---------|-------------|
| `speckit.diagram-gen.update` | Generate/update the CML model from the constitution |

## Lifecycle Hooks

This extension registers an `after_constitution` hook so the diagram is automatically regenerated whenever the constitution is amended.
