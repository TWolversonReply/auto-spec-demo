---
description: Generate a PlantUML component diagram from the Context Mapper CML model showing bounded contexts and their integration relationships.
---

## User Input

```text
$ARGUMENTS
```

## Outline

You are generating a **PlantUML component diagram** from the existing Context Mapper CML model at `model/context-map.cml`, using the Context Mapper CLI's `plantuml` generator. This diagram complements the graphical context map by showing bounded contexts as UML components with typed dependency arrows and integration technology labels.

Follow this execution flow:

1. **Verify the CML model exists** at `model/context-map.cml`.
   - If it does not exist, inform the user and suggest running `/speckit.diagram-gen.update` first.
   - Do NOT generate or modify the CML file — that is the responsibility of `speckit.diagram-gen.update`.

2. **Ensure prerequisites are available**.

   a. **Check for Context Mapper CLI**:
      - Look for `cm` (Linux/macOS) or `cm.bat` (Windows) on `PATH`.
      - If not found, check for a local installation at `./cm-cli/context-mapper-cli-*/bin/cm` or `./cm-cli/context-mapper-cli-*/bin/cm.bat`.
      - If still not found, **install it**:
        ```bash
        # Linux/macOS
        mkdir -p cm-cli && cd cm-cli
        curl -sL https://repo1.maven.org/maven2/org/contextmapper/context-mapper-cli/6.12.0/context-mapper-cli-6.12.0.tar -o cm.tar
        tar xf cm.tar && rm cm.tar
        cd ..
        ```
        ```powershell
        # Windows
        New-Item -ItemType Directory -Force cm-cli | Set-Location
        curl -sL "https://repo1.maven.org/maven2/org/contextmapper/context-mapper-cli/6.12.0/context-mapper-cli-6.12.0.zip" -o cm.zip
        Expand-Archive cm.zip -DestinationPath . -Force; Remove-Item cm.zip
        Set-Location ..
        ```

   b. **Check for Java 17+** (required by Context Mapper CLI):
      - Run `java -version` and verify the major version is ≥ 17.
      - If Java is not available or below v17, inform the user and stop — do not attempt
        to install Java automatically.

3. **Run the PlantUML generator**.

   The Context Mapper CLI `plantuml` generator produces:
   - A **component diagram** (`.puml`) showing all bounded contexts and their relationships
   - **Class diagrams** (`.puml`) for each bounded context that contains tactic DDD content (aggregates, entities)

   ```bash
   mkdir -p docs_assets/images
   ./cm-cli/context-mapper-cli-6.12.0/bin/cm generate \
     -i model/context-map.cml \
     -g plantuml \
     -o docs_assets/images/

   # Context Mapper mirrors input path structure — find and move the component diagram
   COMPONENT_PUML=$(find docs_assets/images -name "*_ComponentDiagram.puml" -type f | head -1)
   if [ -n "$COMPONENT_PUML" ]; then
     mv "$COMPONENT_PUML" docs_assets/images/component-diagram.puml
   fi

   # Move any per-context class diagrams to a predictable location
   mkdir -p docs_assets/images/class-diagrams
   find docs_assets/images -name "*_BC_*_ClassDiagram.puml" -type f -exec mv {} docs_assets/images/class-diagrams/ \;

   # Clean up intermediate directory structure created by Context Mapper
   rm -rf docs_assets/images/model/
   ```

   ```powershell
   New-Item -ItemType Directory -Force docs_assets\images | Out-Null
   & cm-cli\context-mapper-cli-6.12.0\bin\cm.bat generate `
     -i model\context-map.cml `
     -g plantuml `
     -o docs_assets\images\

   # Find and move the component diagram
   $componentPuml = Get-ChildItem docs_assets\images -Recurse -Filter "*_ComponentDiagram.puml" | Select-Object -First 1
   if ($componentPuml) { Move-Item $componentPuml.FullName docs_assets\images\component-diagram.puml -Force }

   # Move per-context class diagrams
   New-Item -ItemType Directory -Force docs_assets\images\class-diagrams | Out-Null
   Get-ChildItem docs_assets\images -Recurse -Filter "*_BC_*_ClassDiagram.puml" | ForEach-Object {
     Move-Item $_.FullName docs_assets\images\class-diagrams\ -Force
   }

   # Clean up intermediate directory structure
   Remove-Item -Recurse docs_assets\images\model -ErrorAction SilentlyContinue
   ```

4. **Render the PlantUML to PNG**.

   Check whether a PlantUML renderer is available, trying in order:

   a. **PlantUML JAR** — check for `plantuml.jar` locally or on PATH:
      ```bash
      if [ ! -f plantuml.jar ]; then
        curl -sL "https://github.com/plantuml/plantuml/releases/download/v1.2024.8/plantuml-1.2024.8.jar" -o plantuml.jar
      fi
      java -jar plantuml.jar -tpng docs_assets/images/component-diagram.puml -o .
      ```
      ```powershell
      if (-not (Test-Path plantuml.jar)) {
        curl -sL "https://github.com/plantuml/plantuml/releases/download/v1.2024.8/plantuml-1.2024.8.jar" -o plantuml.jar
      }
      java -jar plantuml.jar -tpng docs_assets\images\component-diagram.puml -o .
      ```
      The `-o .` flag writes the PNG alongside the `.puml` source.

   b. If PlantUML rendering succeeds, verify `docs_assets/images/component-diagram.png` exists.

   c. If PlantUML rendering fails (no Java, no Graphviz for PlantUML), keep the `.puml` file
      and inform the user that the raw PlantUML source is available for manual rendering
      or for embedding via a PlantUML-compatible viewer (e.g., VS Code PlantUML extension,
      PlantUML Server, or MkDocs plantuml plugin).

5. **Update the constitution** at `.specify/memory/constitution.md`:
   - Locate the `## Sociotechnical Architecture` section.
   - If a component diagram reference already exists, update it in place.
   - If not, append the component diagram **after** the existing context map image, with
     a brief explanatory sentence. Example:

     ```markdown
     The component diagram below shows bounded context dependencies and integration
     technologies at a glance.

     ![Component Diagram](../images/component-diagram.png)
     ```

   - Do NOT remove or modify the existing context map image reference.
   - Do NOT modify any other sections of the constitution.
   - Increment the constitution version (PATCH bump) and update `Last Amended` to today's date.

6. **Update principle VIII** in the constitution if it does not already reference the
   component diagram:
   - Add `component-diagram.png` to the list of generated visual artifacts alongside
     `context-map.png`.
   - Keep the wording concise — e.g., update "A generated visual artifact (currently
     `images/context-map.png`)" to "Generated visual artifacts (currently
     `images/context-map.png` and `images/component-diagram.png`)".

7. **Validation**:
   - `docs_assets/images/component-diagram.puml` MUST exist after generation.
   - `docs_assets/images/component-diagram.png` SHOULD exist (warn if rendering failed).
   - The `.puml` file MUST contain `@startuml` and `@enduml` markers.
   - The `.puml` file MUST reference at least one component matching a BoundedContext
     from the CML model.
   - The constitution MUST reference the component diagram in the Sociotechnical
     Architecture section.

8. **Output a summary** listing:
   - Number of components in the diagram.
   - Whether per-context class diagrams were also generated (and where they are).
   - Whether PNG rendering succeeded or only `.puml` source is available.
   - Constitution version bump.
   - Suggested commit message.
