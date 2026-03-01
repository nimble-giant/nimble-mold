# Skill: System Design

## What This Skill Does

This skill applies structured system design methodology to help users reason through architecture decisions. It systematically discovers existing codebase context, identifies gaps, and produces a comprehensive design document covering components, data, interfaces, cross-cutting concerns, and tradeoff analysis.

The skill produces a saved design document — not implementation. The user decides independently whether and when to act on the findings.

## When to Use This Skill

Use this skill whenever the user wants to design, evaluate, or reason about system architecture. This includes requests like:
- "Design the architecture for X"
- "How should we structure this system?"
- "What's the best approach for building X?"
- "Review our current architecture"
- "Help me think through the technical design for X"
- "We need to refactor the data layer"
- "Plan the system design for this feature"

## Workflow

Follow these steps in order:

### Step 1: Enter Plan Mode

Enter plan mode immediately. The design output IS the plan.

### Step 2: Codebase Discovery

Before producing the design, systematically gather context about the existing system:

1. **Project Structure**: Scan directory layout, entry points, and module boundaries
2. **Technology Stack**: Identify languages, frameworks, package managers, and key dependencies
3. **Architecture Patterns**: Detect patterns in use (MVC, microservices, event-driven, monolith, layered, hexagonal, etc.)
4. **Data Layer**: Find database schemas, ORM models, migrations, and data access patterns
5. **API Surface**: Discover API routes, controllers, GraphQL schemas, or RPC definitions
6. **CI/CD**: Check for pipeline configs (`.github/workflows/`, `.gitlab-ci.yml`, `Jenkinsfile`, `buildkite/`, `.circleci/`)
7. **Configuration**: Examine environment variables, config files, and feature flags
8. **Testing**: Identify test frameworks, coverage patterns, and test organization

### Step 3: Gap Analysis

Evaluate the current state against the design goal:

- **What exists**: Components, services, and patterns already in place
- **What's missing**: Capabilities, integrations, or patterns needed for the goal
- **What conflicts**: Existing patterns or constraints that resist the proposed change
- **What enables**: Existing infrastructure or patterns that support the change

Use the `AskUserQuestion` tool to clarify ambiguities — do not assume. Focus questions on:
- Business constraints not visible in code
- Performance and scale requirements
- Team preferences or organizational standards
- Timeline or phasing expectations

### Step 4: Execute Design Phases

Work through the following phases in order. Each phase builds on the previous ones.

#### Phase 1: Requirements Synthesis

Distill functional and non-functional requirements from the user's request, issue context, and codebase discovery:

- **Functional**: What the system must do — capabilities, features, behaviors
- **Non-functional**: Quality attributes — performance targets, scalability needs, reliability expectations, security requirements, maintainability goals
- **Constraints**: Fixed boundaries — existing infrastructure, team size, compliance requirements, backward compatibility

#### Phase 2: Component Design

Define the high-level system structure:

- **Components**: Major building blocks and their single responsibilities
- **Boundaries**: Where each component starts and ends
- **Interactions**: How components communicate (sync/async, protocols, data formats)
- **Dependencies**: What each component depends on (internal and external)

Include a Mermaid diagram showing component relationships.

#### Phase 3: Data Design

Define how data flows through and persists in the system:

- **Data Models**: Core entities and their relationships
- **Storage**: Database or storage choices with justification
- **Data Flow**: How data moves between components
- **Consistency**: Strategies for data integrity (transactions, eventual consistency, idempotency)

#### Phase 4: Interface Design

Define the contracts between components and with external consumers:

- **APIs**: Endpoints, methods, request/response shapes
- **Events**: Event types, payloads, and routing (if applicable)
- **Protocols**: Communication patterns (REST, gRPC, WebSocket, message queues)

#### Phase 5: Cross-Cutting Concerns

Address system-wide qualities:

- **Error Handling**: Failure modes, retry strategies, degradation paths
- **Observability**: Logging, metrics, tracing, alerting
- **Security**: Authentication, authorization, data protection
- **Performance**: Caching strategies, connection pooling, rate limiting

#### Phase 6: Tradeoff Analysis

For each significant design decision, document:

- **Decision**: What was chosen
- **Alternatives considered**: What else was evaluated
- **Rationale**: Why this option wins given the constraints
- **Risks**: What could go wrong with this choice
- **Mitigations**: How to reduce those risks

### Step 5: Present the Plan

Present the complete system design via `ExitPlanMode` using this structure:

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

### Step 6: Save the Design and Stop

After user approval, save the design document to:
- `.claude/plans/` if a `.claude/` directory exists in the project root
- `~/.claude/plans/` otherwise

Create the `plans/` subdirectory if it does not exist.

Name the file `design-{slug}.md` where `{slug}` is a kebab-case summary of the design goal (3-5 words max).

**Stop after saving.** Do not begin implementation or prompt toward it.

## Rules

- Always enter plan mode. The design output IS the plan.
- Do not skip phases. Every phase must appear in the output.
- Research before designing. Always run codebase discovery before proposing architecture.
- Ask, don't assume. Use `AskUserQuestion` for ambiguities rather than guessing requirements.
- Be direct. Avoid hedge words like "potentially," "might," "could possibly." State tradeoffs clearly.
- Stay grounded. Every design decision should trace back to a requirement, constraint, or codebase finding.
- Include diagrams. Use Mermaid syntax for architecture and data flow diagrams.
- Keep it actionable. The design should be implementable by a developer reading it cold.
- **No implementation.** This skill produces documentation and analysis only. It must never write application code, create features, or modify project source files.
- **Stop after the design.** Do not ask "should I start building this?" or otherwise prompt toward implementation.
