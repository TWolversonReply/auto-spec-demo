---
description: Generate or update the Context Mapper CML model and sociotechnical architecture diagram from the constitution.
---

## User Input

```text
$ARGUMENTS
```

## Outline

You are generating a **Context Mapper CML model** that represents the sociotechnical architecture described in the project constitution, and then rendering it into a context map PNG image locally using the Context Mapper CLI.

Follow this execution flow:

1. **Read the constitution** at `.specify/memory/constitution.md`.
   - Extract the list of **business domains** (these become Bounded Contexts).
   - Extract the list of **teams** and their responsibilities (these become Teams in the CML model).
   - Extract any **relationships between domains** implied by the constitution (e.g., shared data, upstream/downstream dependencies).

2. **Generate the CML model** at `model/context-map.cml`.

   The CML file MUST follow this structure (based on the
   [Context Mapper insurance team-map example](https://contextmapper.org/docs/context-map-generator/)):

   ```cml
   /**
    * Single ORGANIZATIONAL map containing both domain context relationships
    * and team-to-team relationships. This produces a unified diagram showing
    * bounded contexts clustered under the teams that own them.
    */
   ContextMap ProjectMap {
     type = ORGANIZATIONAL
     state = TO_BE

     /* Domain contexts (subsystems/components) */
     contains <DomainName1>Context, <DomainName2>Context, ...

     /* Teams */
     contains <TeamName1>Team, <TeamName2>Team, ...

     /* Domain context relationships */
     <DownstreamContext> [D,CF]<-[U,OHS,PL] <UpstreamContext> {
       implementationTechnology = "Azure Service Bus"
     }

     /* Team relationships — enabling/platform teams support stream-aligned teams */
     <EnablingTeam> [U,S]->[D,C] <StreamAlignedTeam>
   }

   /* Team definitions — each realizes the domain contexts it owns */
   BoundedContext <TeamName>Team realizes <DomainName>Context {
     type = TEAM
     domainVisionStatement = "<team topology role>: <team purpose>"
   }

   /* Domain context definitions */
   BoundedContext <DomainName>Context implements <SubdomainName> {
     type = APPLICATION
     domainVisionStatement = "<description from constitution>"
     responsibilities = "<key responsibilities>"
   }

   /* Domain hierarchy */
   Domain <ProjectName>Domain {
     Subdomain <SubdomainName> {
       type = CORE_DOMAIN
       domainVisionStatement = "<vision>"
     }
   }
   ```

   ### CML Syntax Rules (validated against Context Mapper CLI v6.12.0)

   **File structure:**
   - The CML file MUST contain exactly ONE `ContextMap` block of type `ORGANIZATIONAL`.
   - Use `ORGANIZATIONAL` (not `SYSTEM_LANDSCAPE`) so that both domain contexts AND
     team contexts can appear in the same map. This is the pattern shown in the
     [Context Mapper docs](https://contextmapper.org/docs/context-map-generator/).
   - Do NOT create two `ContextMap` blocks in a single file. The Context Mapper CLI
     `context-map` generator crashes with `BoundedContextAlreadyPartOfContextMapException`
     when two `ContextMap` blocks coexist.
   - All domain contexts AND all team contexts MUST appear in the `contains` list of
     the single `ContextMap` block.

   **Context types:**
   - Each **business domain** → `BoundedContext` with `type = APPLICATION`.
   - Each **team** → `BoundedContext` with `type = TEAM` with `realizes` referencing
     the domain context(s) that team owns.

   **Relationship syntax:**
   - **Domain-to-domain relationships** (upstream/downstream):
     Use `<-` or `->` arrow syntax. The arrow ALWAYS points from upstream to downstream.
     Example: `DownstreamCtx [D,CF]<-[U,OHS,PL] UpstreamCtx`
   - **Team-to-team relationships** (enabling/platform → stream-aligned):
     Use Customer/Supplier: `EnablingTeam [U,S]->[D,C] StreamAlignedTeam`
   - **NEVER** use `<->` for upstream/downstream. `<->` is ONLY for symmetric
     relationships: Partnership `[P]<->[P]` or Shared Kernel `[SK]<->[SK]`.
   - **Downstream roles** (left of `<-`): `CF` (Conformist), `ACL` (Anticorruption Layer),
     `C` (Customer).
   - **Upstream roles** (right of `<-`): `OHS` (Open Host Service), `PL` (Published Language),
     `S` (Supplier).
   - Do NOT place upstream roles on the downstream side or vice versa.
   - Every context referenced in a relationship MUST be in the `contains` list.

   **Other rules:**
   - Use `implementationTechnology` on domain-to-domain relationships where relevant.
   - Enabling/platform teams should `realizes` all domain contexts they support.
   - Stream-aligned teams should `realizes` the specific domain context(s) they own.
   - Include a `Domain` block with `Subdomain` entries matching the constitution.
   - Subdomain types: `CORE_DOMAIN`, `SUPPORTING_DOMAIN`, `GENERIC_SUBDOMAIN`.
   - BoundedContext names must be unique across the entire CML file.
   - Prefix team `domainVisionStatement` values with the team topology role
     (e.g. "Enabling team:", "Platform team:", "Stream-aligned:").

   **Formatting (important for CLI compatibility):**
   - Place the header comment (`/** ... */`) BEFORE the `ContextMap` block, not inside it.
   - Do NOT use `/* */` block comments inside the `ContextMap` block body. The Context
     Mapper CLI v6.12.0 can crash when block comments appear between relationship
     definitions. Use `//` line comments if needed, or omit comments inside the block.
   - Keep the `ContextMap` block clean: only `contains`, relationship definitions, and
     `//` line comments.

3. **Render the context map image locally** using the Context Mapper CLI.

   Before running the generator, ensure the tool is available:

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

   c. **Check for Graphviz** (required for PNG generation):
      - Run `dot -V` to verify Graphviz is installed.
      - If not found:
        - Linux: `sudo apt-get install -y graphviz`
        - macOS: `brew install graphviz`
        - Windows: `winget install --id Graphviz.Graphviz --accept-package-agreements` or
          `choco install graphviz -y`
      - If installation fails, inform the user and stop.

   d. **Run the generator**:
      ```bash
      mkdir -p docs_assets/images
      ./cm-cli/context-mapper-cli-6.12.0/bin/cm generate \
        -i model/context-map.cml \
        -g context-map \
        -o docs_assets/images/

      # Context Mapper mirrors input path structure, creating a model/ subdir
      # and names output as {file}_{MapName}.png — move and rename to stable reference
      mv docs_assets/images/model/context-map_*.png docs_assets/images/context-map.png 2>/dev/null || \
        mv docs_assets/images/context-map_*.png docs_assets/images/context-map.png 2>/dev/null || true
      # Clean up intermediate files
      rm -rf docs_assets/images/model/
      rm -f docs_assets/images/*.svg docs_assets/images/*.gv
      ```
      ```powershell
      New-Item -ItemType Directory -Force docs_assets\images | Out-Null
      & cm-cli\context-mapper-cli-6.12.0\bin\cm.bat generate `
        -i model\context-map.cml `
        -g context-map `
        -o docs_assets\images\

      # Context Mapper mirrors input path structure, creating a model\ subdir
      # and names output as {file}_{MapName}.png — move and rename to stable reference
      $png = Get-ChildItem docs_assets\images -Recurse -Filter "context-map_*.png" | Select-Object -First 1
      if ($png) { Move-Item $png.FullName docs_assets\images\context-map.png -Force }
      # Clean up intermediate files
      Remove-Item -Recurse docs_assets\images\model -ErrorAction SilentlyContinue
      Remove-Item docs_assets\images\*.svg, docs_assets\images\*.gv -ErrorAction SilentlyContinue
      ```

   e. **Verify** the file `docs_assets/images/context-map.png` was created successfully.
      If the generator fails, output the full error message for diagnosis.

   f. **Clean up** — the cleanup is handled in step (d) above. No additional action needed.

4. **Update the constitution** at `.specify/memory/constitution.md`:
   - Add a new section `## Sociotechnical Architecture` immediately before the `## Governance` section (if it doesn't already exist; if it does, update it in place).
   - This section is for **the reader** — it explains the team topology and business domain structure. It MUST contain:
     - A brief narrative explaining how teams are organised around business domains (team topologies: stream-aligned, enabling, platform)
     - A summary of the bounded contexts and which teams own them
     - The context map image: `![Sociotechnical Context Map](../images/context-map.png)`
     - A sentence noting that the diagram is derived from the formal model at `[model/context-map.cml](../model/context-map.cml)`
   - The tone should be **explanatory for stakeholders** — describe *what* the architecture is, not *how* the diagram was generated. Do NOT mention CI/CD pipelines, generation workflows, or tooling instructions in this section.
   - Do NOT include Mermaid diagrams — the context map image is generated by Context Mapper CLI.
   - Do NOT modify any other sections of the constitution.
   - Increment the constitution version (PATCH bump) and update `Last Amended` to today's date.

5. **Validation**:
   - Every business domain from the constitution MUST appear as a BoundedContext.
   - Every team from the constitution MUST appear as a Team BoundedContext.
   - Every team MUST realize at least one domain context.
   - The CML file MUST be syntactically valid Context Mapper DSL.
   - The file `docs_assets/images/context-map.png` MUST exist after generation.

6. **Output a summary** listing:
   - Number of bounded contexts and teams modelled.
   - Relationships defined.
   - Constitution version bump.
   - Whether Context Mapper CLI was already installed or freshly downloaded.
   - Suggested commit message.
