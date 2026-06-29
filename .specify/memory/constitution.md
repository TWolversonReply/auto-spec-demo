<!-- Sync Impact Report
  Version change: N/A → 1.0.0
  Modified principles: N/A (initial creation)
  Added sections: Core Principles (7), Business Domains, Team Structure & Governance, Governance
  Removed sections: None
  Templates requiring updates: ⚠ pending (no feature specs yet)
  Follow-up TODOs: None
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

### V. Team Accessibility

Integration patterns and tooling MUST be accessible to business domain teams with varying technical capability. Self-service capabilities (e.g., templated Logic Apps, standardised connectors, documented APIs) MUST be provided so domain teams can build and maintain their own integrations with guidance from the Integration team. Documentation MUST be clear, example-driven, and free of unnecessary jargon.

### VI. Observable by Default

Every integration flow MUST emit structured telemetry (logs, metrics, traces) to a centralised observability platform. Failures MUST produce actionable alerts with sufficient context for diagnosis. End-to-end message tracing across domain boundaries MUST be supported from day one of each migrated flow.

### VII. Infrastructure as Code

All Azure resources MUST be provisioned and managed through infrastructure-as-code (Bicep or Terraform). No manual portal changes in production environments. Environment promotion (dev → test → prod) MUST follow a consistent, automated pipeline managed by the Infrastructure team.

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
| **Integration** | Owns migration execution, defines patterns, builds shared components, supports domain teams |
| **Database Administration** | Manages Oracle-to-Azure data migration, ensures data integrity during transition |
| **Infrastructure** | Provisions Azure landing zones, manages networking/security, maintains CI/CD pipelines |
| **Business Domain Teams** | Own domain-specific integration logic, validate migrated flows, adopt self-service tooling |

Domain teams MUST be supported with training, templates, and pairing sessions. The Integration team MUST NOT become a bottleneck — their role is to enable, not gatekeep.

## Governance

- This constitution supersedes all other integration development practices for the migration programme.
- Amendments require: documented rationale, review by Integration team lead, and a migration impact assessment.
- All pull requests MUST verify compliance with these principles.
- Architecture Decision Records (ADRs) MUST be created for any deviation from stated principles.
- Quarterly reviews MUST assess migration progress, cost reduction achieved, and principle adherence.

**Version**: 1.0.0 | **Ratified**: 2026-06-29 | **Last Amended**: 2026-06-29
