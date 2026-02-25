# Architect

## Purpose

This command takes a design goal or feature description and produces a structured system design document. It systematically discovers existing codebase context, identifies architectural gaps, and works through six design phases — requirements, components, data, interfaces, cross-cutting concerns, and tradeoff analysis — to produce a comprehensive, implementable design.

**This command produces supporting artifacts only — not implementation.** The output is a design document (and optionally ADR files) that helps the team align on architecture before writing code. The user takes implementation as a separate, deliberate act.

## Command Name

`architect`

## Skill Dependency

This command requires the **system-design** skill. Before executing the workflow below, load and apply the full codebase discovery protocol, gap analysis protocol, and design phases defined in `.claude/skills/system-design.md`.

## Invocation Syntax

```bash
/architect [flags] <design goal or feature description>
```

### Flags

- `--adr`: Generate individual Architecture Decision Record (ADR) files for each significant design decision

### Examples

```bash
# Basic system design
/architect add real-time notifications to the platform

# Design with ADR generation
/architect --adr migrate from monolith to microservices for the billing domain

# Feature architecture
/architect design the authentication and authorization layer for multi-tenant support

# Refactoring design
/architect restructure the data pipeline to support streaming ingestion
```

## Workflow

When this command is invoked, Claude must:

1. **Enter Plan Mode** immediately
2. **Run Codebase Discovery** following the protocol in the system-design skill
3. **Run Gap Analysis** following the protocol in the system-design skill
4. **Execute all six design phases** from the system-design skill against the user's goal
5. **Present the complete design** as the plan using `ExitPlanMode`
6. **Wait for user approval**
7. After approval:
   - Save the design document
   - If `--adr` flag is present, generate ADR files for each tradeoff analysis entry

### Design Save Location

Save the final design document to:
- `.claude/plans/` if a `.claude/` directory exists in the project root
- `~/.claude/plans/` otherwise

Create the `plans/` subdirectory if it does not exist.

### Design File Naming

Name the file `design-{slug}.md` where `{slug}` is a kebab-case summary of the design goal (3-5 words max), derived from the core goal — not randomly generated.

Examples:
- "add real-time notifications to the platform" → `design-realtime-notifications.md`
- "migrate from monolith to microservices for billing" → `design-billing-microservices.md`
- "authentication and authorization for multi-tenant" → `design-multitenant-auth.md`

---

## Design Methodology

Claude must work through the following phases in order. Each phase builds on the previous ones. The methodology is defined in full in the system-design skill (`.claude/skills/system-design.md`).

### Phase 1: Requirements Synthesis

Distill functional and non-functional requirements from the user's description, issue context, and codebase discovery:

- **Functional**: What the system must do — capabilities, features, behaviors
- **Non-functional**: Quality attributes — performance targets, scalability needs, reliability expectations, security requirements, maintainability goals
- **Constraints**: Fixed boundaries — existing infrastructure, team size, compliance requirements, backward compatibility

### Phase 2: Component Design

Define the high-level system structure:

- **Components**: Major building blocks and their single responsibilities
- **Boundaries**: Where each component starts and ends
- **Interactions**: How components communicate (sync/async, protocols, data formats)
- **Dependencies**: What each component depends on (internal and external)

Include a Mermaid diagram showing component relationships.

### Phase 3: Data Design

Define how data flows through and persists in the system:

- **Data Models**: Core entities and their relationships
- **Storage**: Database or storage choices with justification
- **Data Flow**: How data moves between components
- **Consistency**: Strategies for data integrity (transactions, eventual consistency, idempotency)

### Phase 4: Interface Design

Define the contracts between components and with external consumers:

- **APIs**: Endpoints, methods, request/response shapes
- **Events**: Event types, payloads, and routing (if applicable)
- **Protocols**: Communication patterns (REST, gRPC, WebSocket, message queues)

### Phase 5: Cross-Cutting Concerns

Address system-wide qualities:

- **Error Handling**: Failure modes, retry strategies, degradation paths
- **Observability**: Logging, metrics, tracing, alerting
- **Security**: Authentication, authorization, data protection
- **Performance**: Caching strategies, connection pooling, rate limiting

### Phase 6: Tradeoff Analysis

For each significant design decision, document:

- **Decision**: What was chosen
- **Alternatives considered**: What else was evaluated
- **Rationale**: Why this option wins given the constraints
- **Risks**: What could go wrong with this choice
- **Mitigations**: How to reduce those risks

---

## Plan Mode Output Format

The plan presented via `ExitPlanMode` must follow this exact structure:

```markdown
# System Design: {short title derived from the goal}

## Overview

{One-paragraph summary of the design goal and the proposed approach.}

## Requirements

### Functional
- {requirement}

### Non-Functional
- {quality attribute}: {target or expectation}

### Constraints
- {constraint}

## Codebase Context

{Summary of relevant existing architecture, patterns, and gaps discovered during codebase discovery. What exists, what's missing, what conflicts, what enables.}

## Component Design

{Component descriptions with responsibilities and boundaries.}

{Mermaid diagram showing component relationships}

## Data Design

{Data models, storage decisions, data flow, and consistency strategies.}

## Interface Design

{API contracts, event schemas, and communication protocols.}

## Cross-Cutting Concerns

| Concern | Strategy |
|---------|----------|
| Error Handling | {approach} |
| Observability | {approach} |
| Security | {approach} |
| Performance | {approach} |

## Tradeoff Analysis

| Decision | Alternatives | Rationale | Risks |
|----------|-------------|-----------|-------|
| {choice} | {options considered} | {why this wins} | {what could go wrong} |

## Implementation Plan

### Phase 1: {name}
- [ ] {task}
- [ ] {task}

### Phase 2: {name}
- [ ] {task}
- [ ] {task}

{Repeat for each phase}

## Open Questions

- {Unresolved question requiring stakeholder input}
```

---

## ADR Generation

When the `--adr` flag is specified, generate individual Architecture Decision Record files after the design document is saved.

### ADR Save Location

Save ADR files to `docs/adr/` in the project root. Create the directory if it does not exist.

### ADR File Naming

Name files `{number}-{slug}.md` where:
- `{number}` is a zero-padded sequence number starting after the highest existing ADR (or `0001` if none exist)
- `{slug}` is a kebab-case summary of the decision (3-5 words max)

Examples:
- `0001-use-postgres-for-billing.md`
- `0002-event-driven-notifications.md`
- `0003-jwt-for-service-auth.md`

### ADR Format

Each ADR must follow this structure:

```markdown
# ADR-{number}: {Decision Title}

## Status

Proposed

## Context

{What is the issue or design question that motivated this decision? Reference the system design document.}

## Decision

{What was decided and why. Be specific about what is being adopted.}

## Alternatives Considered

{What other options were evaluated, and why they were not chosen.}

## Consequences

### Positive
- {benefit}

### Negative
- {cost or risk}

### Mitigations
- {how negative consequences will be managed}
```

### ADR Generation Rules

- Generate one ADR per row in the Tradeoff Analysis table
- Only generate ADRs for decisions that are architecturally significant — skip trivial or obvious choices
- Set status to "Proposed" — the team accepts or supersedes ADRs separately
- Reference the parent design document in the Context section

---

## Research Requirements

Before producing the design, Claude must gather relevant context following the codebase discovery and gap analysis protocols from the system-design skill:

- **Codebase context**: Explore the project structure, architecture patterns, data layer, API surface, CI/CD, and testing
- **Gap analysis**: Evaluate what exists, what's missing, what conflicts, and what enables the design goal
- **Web research**: Search for relevant architectural patterns, prior art, or libraries when the design involves unfamiliar domains
- **Clarification**: Use `AskUserQuestion` to resolve ambiguities rather than assuming

Do not fabricate architectural claims or assert that patterns exist without verifying in the codebase.

---

## Integration Notes

This command works well in combination with other nimble-mold commands:

- **`/preflight`**: Validate the implementation plan against INVEST criteria before starting work
- **`/create-issue`**: Generate issues from the implementation plan tasks in the design document
- **`/start-issue`**: Begin implementing individual tasks from the plan

A typical workflow: `/architect` → approve design → `/create-issue` for each phase → `/start-issue` to implement.

---

## Rules

- Always enter plan mode. The design output IS the plan.
- Do not skip phases. Every design phase must appear in the output.
- Research before designing. Always run codebase discovery and gap analysis before proposing architecture.
- Ask, don't assume. Use `AskUserQuestion` for ambiguities rather than guessing requirements.
- Be direct. Avoid hedge words. State tradeoffs clearly with concrete rationale.
- Stay grounded. Every design decision should trace back to a requirement, constraint, or codebase finding.
- Include diagrams. Use Mermaid syntax for architecture and data flow diagrams.
- Keep it actionable. The design should be implementable by a developer reading it cold.
- **No implementation.** This command produces documentation and analysis only — design documents, ADRs, and diagrams. It must never write application code, create features, or modify project source files.
- **Stop after saving.** After the user approves the plan, save the design document (and ADRs if flagged) and stop. Do not ask "should I start building this?" or otherwise prompt toward implementation.

## Recap

Use this command to produce a structured system design before committing to implementation. The architect command discovers existing codebase context, identifies gaps, and works through six design phases to produce a comprehensive architecture document. Optionally generates ADR files for significant decisions. The output is saved documentation — design documents and decision records that support team alignment. It does not produce implementation. The user decides independently whether and when to act on the design.
