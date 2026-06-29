---
description: Generate or update the Context Mapper CML model and sociotechnical architecture diagram from the constitution.
---

## User Input

```text
$ARGUMENTS
```

## Outline

You are generating a **Context Mapper CML model** that represents the sociotechnical architecture described in the project constitution. The CML model is then rendered into a context map image by the CI/CD pipeline using the Context Mapper CLI.

Follow this execution flow:

1. **Read the constitution** at `.specify/memory/constitution.md`.
   - Extract the list of **business domains** (these become Bounded Contexts).
   - Extract the list of **teams** and their responsibilities (these become Teams in the CML model).
   - Extract any **relationships between domains** implied by the constitution (e.g., shared data, upstream/downstream dependencies).

2. **Generate the CML model** at `model/context-map.cml`.

   The CML file MUST follow this structure:

   The CML file MUST follow this structure:

   ```cml
   /**
    * SYSTEM_LANDSCAPE map — domain bounded contexts and their relationships.
    * Only APPLICATION/FEATURE/SYSTEM contexts go in the contains list.
    * TEAM contexts are defined separately OUTSIDE any ContextMap block.
    */
   ContextMap ProjectContextMap {
     type = SYSTEM_LANDSCAPE
     state = TO_BE

     contains <BoundedContext1>, <BoundedContext2>, ...

     // Upstream-downstream: arrow points from upstream to downstream
     <DownstreamContext> [D,CF]<-[U,OHS,PL] <UpstreamContext> {
       implementationTechnology = "Azure Service Bus"
     }
   }

   // One BoundedContext per business domain
   BoundedContext <DomainName>Context implements <SubdomainName> {
     type = APPLICATION
     domainVisionStatement = "<description from constitution>"
     responsibilities = "<key responsibilities>"
   }

   // Domain hierarchy
   Domain <ProjectName>Domain {
     Subdomain <SubdomainName> {
       type = CORE_DOMAIN
       domainVisionStatement = "<vision>"
     }
   }

   // One BoundedContext per team — standalone, NOT inside any ContextMap block
   BoundedContext <TeamName>Team realizes <DomainName>Context {
     type = TEAM
     domainVisionStatement = "<team role from constitution>"
   }
   ```

   ### CML Syntax Rules (validated against Context Mapper CLI v6.12.0)

   **File structure:**
   - The CML file MUST contain exactly ONE `ContextMap` block of type `SYSTEM_LANDSCAPE`.
   - Do NOT create a second `ContextMap` block (e.g. `ORGANIZATIONAL`). The Context Mapper
     CLI `context-map` generator crashes with `BoundedContextAlreadyPartOfContextMapException`
     when two `ContextMap` blocks coexist in a single file.
   - TEAM bounded contexts are defined as standalone `BoundedContext` declarations outside
     the `ContextMap` block. They use `realizes` to link to the domain contexts they own.
     This preserves team-to-context ownership in the model without requiring a second map.

   **Context types:**
   - Each **business domain** → `BoundedContext` with `type = APPLICATION`.
   - Each **team** → `BoundedContext` with `type = TEAM` with `realizes`.
   - TEAM contexts MUST NOT appear in the `contains` list of a `SYSTEM_LANDSCAPE` map.

   **Relationship syntax:**
   - **Upstream/Downstream**: Use `<-` or `->` arrow syntax. The arrow ALWAYS points
     from upstream to downstream.
     Example: `DownstreamCtx [D,CF]<-[U,OHS,PL] UpstreamCtx`
   - **NEVER** use `<->` for upstream/downstream. `<->` is ONLY for symmetric
     relationships: Partnership `[P]<->[P]` or Shared Kernel `[SK]<->[SK]`.
   - **Downstream roles** (left of `<-`): `CF` (Conformist), `ACL` (Anticorruption Layer).
   - **Upstream roles** (right of `<-`): `OHS` (Open Host Service), `PL` (Published Language).
   - Do NOT place upstream roles on the downstream side or vice versa.
   - Every context referenced in a relationship MUST be in the `contains` list.

   **Other rules:**
   - Use `implementationTechnology` to note the target platform where relevant.
   - The Integration team should `realizes` shared/cross-cutting contexts.
   - Include a `Domain` block with `Subdomain` entries matching the constitution.
   - Subdomain types: `CORE_DOMAIN`, `SUPPORTING_DOMAIN`, `GENERIC_SUBDOMAIN`.
   - BoundedContext names must be unique across the entire CML file.
   - The `=` sign in attribute assignments (e.g. `type = APPLICATION`) is optional in CML
     but should be used consistently for readability.

3. **Update the constitution** at `.specify/memory/constitution.md`:
   - Add a new section `## Sociotechnical Architecture` immediately before the `## Governance` section.
   - This section MUST contain:
     - A brief explanation that this diagram is generated from the Context Mapper CML model by the CI/CD pipeline.
     - An image reference to the generated context map: `![Sociotechnical Context Map](../images/context-map.png)`
     - A link to the CML source: `Source: [model/context-map.cml](../model/context-map.cml)`
   - Do NOT include Mermaid diagrams — the context map image is generated by Context Mapper CLI in the build pipeline.
   - Do NOT modify any other sections of the constitution.
   - Increment the constitution version (PATCH bump) and update `Last Amended` to today's date.

4. **Validation**:
   - Every business domain from the constitution MUST appear as a BoundedContext.
   - Every team from the constitution MUST appear as a Team BoundedContext.
   - Every team MUST realize at least one domain context.
   - The CML file MUST be syntactically valid Context Mapper DSL.

5. **Output a summary** listing:
   - Number of bounded contexts and teams modelled.
   - Relationships defined.
   - Constitution version bump.
   - Suggested commit message.
