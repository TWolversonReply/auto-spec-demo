# Sociotechnical Diagram Generator Extension

This extension generates **sociotechnical architecture diagrams** that map organisational teams to DDD bounded contexts, derived from the project constitution.

## How It Works

1. Reads the constitution at `.specify/memory/constitution.md`
2. Generates (or updates) a Context Mapper CML model at `model/context-map.cml`
3. The CML model defines bounded contexts for each business domain and maps teams to those contexts
4. Renders a **graphical context map** (PNG) showing team-to-context ownership and relationships
5. Renders a **PlantUML component diagram** (PNG) showing bounded context dependencies with integration technology labels

## Commands

| Command | Description |
|---------|-------------|
| `speckit.diagram-gen.update` | Generate/update the CML model and graphical context map from the constitution |
| `speckit.diagram-gen.component` | Generate a PlantUML component diagram from the CML model |

## Generated Artifacts

| Artifact | Path | Description |
|----------|------|-------------|
| Context Map (Graphviz) | `docs_assets/images/context-map.png` | Sociotechnical team map with ownership clusters |
| Component Diagram (PlantUML) | `docs_assets/images/component-diagram.png` | UML component diagram showing integration dependencies |
| Component Diagram Source | `docs_assets/images/component-diagram.puml` | Raw PlantUML source for further editing or rendering |
| Class Diagrams (PlantUML) | `docs_assets/images/class-diagrams/*.puml` | Per-context class diagrams (when tactic DDD content exists in CML) |

## Lifecycle Hooks

This extension registers two `after_constitution` hooks so both diagrams are automatically regenerated whenever the constitution is amended:

1. `speckit.diagram-gen.update` (priority 5) — regenerates the CML model and context map
2. `speckit.diagram-gen.component` (priority 10) — regenerates the PlantUML component diagram

## Prerequisites

- **Java 17+** — required by Context Mapper CLI
- **Graphviz** — required for context map PNG rendering (`dot -V` to verify)
- **PlantUML** — downloaded automatically if not present; requires Java and Graphviz
