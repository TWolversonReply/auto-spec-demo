<!-- Sync Impact Report
  Version change: 2.0.1 -> 2.0.2
  Modified principles:
    - V. Team Ownership Accessibility (clarified business-system ownership plus central-team enablement)
    - VII. Infrastructure as Code Ownership (clarified domain ownership with central infrastructure stewardship)
    - Team Structure & Responsibilities (restored central teams)
    - Sociotechnical Architecture (restored stream-aligned + enabling/platform model)
  Added sections: None
  Removed sections: None
  Templates requiring updates:
    - ✅ .specify/templates/plan-template.md (reviewed, no constitution-specific team references)
    - ✅ .specify/templates/spec-template.md (reviewed, no constitution-specific team references)
    - ✅ .specify/templates/tasks-template.md (reviewed, no constitution-specific team references)
    - ✅ .specify/templates/commands/*.md (no command templates present in repository)
    - ✅ README.md (reviewed, no conflicting team-ownership statements)
  Follow-up TODOs:
    - Run /speckit.diagram-gen.update to regenerate sociotechnical diagrams after constitution/model changes
    - Ensure CI pipeline regenerates and publishes the diagram artifact at images/context-map.png
-->
# Contoso University Integration Migration Constitution

## Core Principles

### I. Cloud-First Migration

All integration workloads MUST target Azure Integration Services as the destination platform. No new development or enhancement work shall be performed on the legacy SoftwareAG WebMethods platform. Every integration pattern implemented MUST have a clear Azure-native equivalent (Logic Apps, Service Bus, API Management, Event Grid, or Functions).

### II. Domain-Driven Integration Boundaries

Integration services MUST be organised around the six core business domains: Students, Staff, Payments, Courses, Awards, and Apprenticeships. Each domain MUST own its integration contracts and message schemas. Cross-domain integration MUST occur through well-defined, versioned contracts — never through shared databases or direct service coupling.

### III. Incremental Migration

Migration MUST proceed domain-by-domain, not as a big-bang cutover. Each domain migration MUST be independently deployable and reversible. Coexistence between legacy (WebMethods) and target (Azure) platforms MUST be supported during transition via an anti-corruption layer or message bridge pattern.

### IV. Cost-Driven Decision Making

Architectural decisions MUST prioritise elimination of Oracle licence costs and on-premises infrastructure burden. Serverless and consumption-based Azure services MUST be preferred over provisioned capacity where workload patterns allow. Total cost of ownership (including operational overhead) MUST be evaluated for every technology choice.

### V. Team Ownership Accessibility

Integration patterns and tooling MUST be accessible to the three owning business-system teams: Student Services, HR Systems, and Awards. Self-service capabilities (e.g., templated Logic Apps, standardised connectors, documented APIs) MUST be provided so these teams can build and maintain their own integrations with enablement from central teams where needed. Documentation MUST be clear, example-driven, and free of unnecessary jargon.

### VI. Observable by Default

Every integration flow MUST emit structured telemetry (logs, metrics, traces) to a centralised observability platform. Failures MUST produce actionable alerts with sufficient context for diagnosis. End-to-end message tracing across domain boundaries MUST be supported from day one of each migrated flow.

### VII. Infrastructure as Code Ownership

All Azure resources MUST be provisioned and managed through infrastructure-as-code (Bicep or Terraform). No manual portal changes in production environments. Environment promotion (dev -> test -> prod) MUST follow a consistent, automated pipeline owned by the team that owns the affected domain, with central stewardship from the Infrastructure team.

### VIII. Sociotechnical Architecture as Source of Truth

Sociotechnical architecture documentation MUST be maintained as executable source, with `model/context-map.cml` as the canonical model for domain boundaries, relationships, and team ownership. Any change to domains, ownership, or integration boundaries MUST update the CML model in the same change set. A generated visual artifact (currently `images/context-map.png`) MUST be regenerated from the model and published with documentation updates. Manual edits to generated diagram artifacts are forbidden.

## Business Domains

The following domains represent the integration boundaries for migration planning:

- **Students** — Enrolment, registration, records, progression
- **Staff** — HR, payroll interfaces, workforce management
- **Payments** — Fee collection, bursaries, financial transactions
- **Courses** — Catalogue, scheduling, learning management system integration
- **Awards** — Certification, graduation, external awarding body interfaces
- **Apprenticeships** — Employer engagement, funding bodies (ESFA), compliance reporting

## Team Structure & Responsibilities

| Team | Role in Migration |
|------|-------------------|
| **Student Services** | Owns Students, Courses, Payments, and Apprenticeships domain integration logic, validation, and operations |
| **HR Systems** | Owns Staff domain integration logic, validation, and operations |
| **Awards** | Owns Awards domain integration logic, validation, and operations |
| **Integration** | Enabling central team; defines migration standards, shared components, and pairing support across all domains |
| **Infrastructure** | Central platform team; owns landing zones, security guardrails, and CI/CD platform capabilities |
| **Database Administration** | Central data platform team; owns Oracle-to-Azure data migration standards and data integrity controls |

Business-system ownership and central-team enablement are both authoritative and MUST be reflected in `model/context-map.cml`.

## Sociotechnical Architecture

This sociotechnical context map is generated from the Context Mapper CML source model by the CI/CD pipeline to keep team ownership and domain boundaries consistent with the constitution.

- **Stream-aligned business-system teams**: Student Services, HR Systems, and Awards own domain integration delivery.
- **Enabling central team**: Integration accelerates delivery through standards, templates, and shared capabilities.
- **Platform central teams**: Infrastructure and Database Administration provide foundational platform and data migration capabilities consumed by business-system teams.

![Sociotechnical Context Map](../images/context-map.png)

Source: [model/context-map.cml](../model/context-map.cml)

## Governance

- This constitution supersedes all other integration development practices for the migration programme.
- Amendments require: documented rationale, review by representatives of Student Services, HR Systems, Awards, Integration, Infrastructure, and Database Administration teams, and a migration impact assessment.
- All pull requests MUST verify compliance with these principles.
- Architecture Decision Records (ADRs) MUST be created for any deviation from stated principles.
- Pull requests that modify business domains, team ownership, or boundaries MUST include updated sociotechnical model and regenerated diagrams.
- Speckit constitution updates MUST trigger the diagram generation hook (`speckit.diagram-gen.update`) before completion.
- Quarterly reviews MUST assess migration progress, cost reduction achieved, and principle adherence.

**Version**: 2.0.2 | **Ratified**: 2026-06-29 | **Last Amended**: 2026-06-29
