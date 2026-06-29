---
description: Generate or update the Context Mapper CML model and sociotechnical architecture diagram from the constitution.
---

## User Input

```text
$ARGUMENTS
```

## Outline

You are generating a **Context Mapper CML model** that represents the sociotechnical architecture described in the project constitution. This model correlates **organisational teams** to **DDD bounded contexts** (business domains).

Follow this execution flow:

1. **Read the constitution** at `.specify/memory/constitution.md`.
   - Extract the list of **business domains** (these become Bounded Contexts).
   - Extract the list of **teams** and their responsibilities (these become Teams in the CML model).
   - Extract any **relationships between domains** implied by the constitution (e.g., shared data, upstream/downstream dependencies).

2. **Generate the CML model** at `model/context-map.cml`.

   The CML file MUST follow this structure:

   ```cml
   ContextMap ProjectContextMap {
     type = SYSTEM_LANDSCAPE
     state = AS_IS

     contains <BoundedContext1>, <BoundedContext2>, ...

     // Define relationships between contexts
     <BoundedContext1> [D,CF]-> <BoundedContext2> {
       implementationTechnology = "Azure Service Bus"
     }
   }

   // One BoundedContext per business domain
   BoundedContext <DomainName>Context {
     type = FEATURE
     domainVisionStatement = "<description from constitution>"
     responsibilities = "<key responsibilities>"
   }

   // One Team per organisational team, with bounded context assignments
   BoundedContext <TeamName>Team realizes <DomainName>Context {
     type = TEAM
     domainVisionStatement = "<team role from constitution>"
   }
   ```

   Rules for the CML model:
   - Each **business domain** from the constitution becomes a `BoundedContext` with `type = FEATURE`.
   - Each **team** becomes a `BoundedContext` with `type = TEAM` that `realizes` the domain contexts it is responsible for.
   - Use `implementationTechnology` to note the target platform (e.g., "Azure Integration Services") where relevant.
   - Where the constitution describes cross-domain interactions, model these as relationships (Upstream/Downstream, Partnership, Shared Kernel, etc.).
   - The Integration team should realize shared/cross-cutting contexts.
   - Use the standard Context Mapper relationship types: `[U,OHS,PL]->`, `[D,CF]->`, `[D,ACL]->`, `Partnership`, `Shared-Kernel`.

3. **Update the constitution** at `.specify/memory/constitution.md`:
   - Add a new section `## Sociotechnical Architecture` immediately before the `## Governance` section.
   - This section MUST contain:
     - A brief explanation that this diagram is auto-generated from the CML model.
     - A Mermaid diagram (` ```mermaid `) showing teams mapped to bounded contexts. Use a flowchart or graph diagram. Example:

       ```mermaid
       graph TB
         subgraph "Integration Team"
           IT[Integration Services]
         end
         subgraph "Domain: Students"
           SC[Students Context]
         end
         IT -->|maintains| SC
       ```

     - A reference to the full Context Mapper model: `See [model/context-map.cml](../model/context-map.cml) for the full DDD context map.`
   - Do NOT modify any other sections of the constitution.
   - Increment the constitution version (PATCH bump) and update `Last Amended` to today's date.

4. **Validation**:
   - Every business domain from the constitution MUST appear as a BoundedContext.
   - Every team from the constitution MUST appear as a Team BoundedContext.
   - Every team MUST realize at least one domain context.
   - The CML file MUST be syntactically valid Context Mapper DSL.
   - The Mermaid diagram MUST render correctly (no syntax errors).

5. **Output a summary** listing:
   - Number of bounded contexts and teams modelled.
   - Relationships defined.
   - Constitution version bump.
   - Suggested commit message.
