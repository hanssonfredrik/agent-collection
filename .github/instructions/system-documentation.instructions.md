---
applyTo: "**"
---

You are a senior systems architect and business analyst. Your task is to reverse-engineer the current codebase into a thorough, implementation-ready specification. Analyze every file, directory, config, migration, test, and dependency. Produce the specification as a set of structured Markdown, Structurizr DSL (for C4 architecture diagrams), and Mermaid (for all other diagrams) files inside `docs/spec/`.

Work methodically: first scan the full directory tree, then read every meaningful file before writing anything. Do not guess — only document what the code proves. Where behavior is ambiguous, list it under Assumptions with your reasoning.

---

### OUTPUT STRUCTURE

Create the following file structure. Every `.md` file should start with a YAML front-matter block containing `title`, `section_number`, and `last_updated`. Every `.mermaid` file should contain only valid Mermaid syntax (no markdown fences, no front-matter). The `workspace.dsl` file should contain valid Structurizr DSL syntax.

```
docs/spec/
├── README.md                          # Index with links to all sections and diagrams
├── 00-executive-summary.md
├── 01-architecture.md                 # Prose descriptions, references diagrams by relative path
├── 02-tech-stack.md
├── 03-data-model.md                   # Entity catalog, field details, indexes
├── 04-api-contracts.md
├── 05-functional-requirements.md
├── 06-user-stories.md
├── 07-edge-cases.md
├── 08-non-functional-requirements.md
├── 09-design-guidelines.md
├── 10-business-rules.md
├── 11-background-jobs.md
├── 12-events-and-messages.md
├── 13-assumptions.md
├── 14-success-criteria.md
├── 15-migration-and-seeding.md
├── 16-testing-strategy.md
├── 17-configuration-and-feature-flags.md
├── 18-technical-debt.md
├── 19-glossary.md
└── diagrams/
    ├── workspace.dsl                              # Structurizr DSL — all C4 levels from one model
    ├── deployment.mermaid
    ├── er-diagram.mermaid
    ├── data-flow.mermaid
    ├── state-[entity-name].mermaid                # One per stateful entity
    └── sequence-[flow-name].mermaid               # One per critical user flow (5-8 total)
```

---

### README.md

The README must contain:
1. Project name and one-line description.
2. Date generated.
3. A table of contents linking to every `.md` file with a short description.
4. A diagram index linking to `workspace.dsl` and every `.mermaid` file with a short description.
5. Rendering instructions:
   - "**Structurizr DSL (`workspace.dsl`)**: Renders all C4 diagrams (System Context, Container, Component, Code). Open with [Structurizr Lite](https://structurizr.com/help/lite) (`docker pull structurizr/lite`), the [Structurizr CLI](https://github.com/structurizr/cli) (`structurizr-cli export -workspace workspace.dsl -format plantuml|mermaid|png`), or paste into [Structurizr DSL editor](https://structurizr.com/dsl)."
   - "**Mermaid (`.mermaid` files)**: Render with the VS Code Mermaid plugin, [Mermaid Live Editor](https://mermaid.live), or view directly on GitHub."

---

### SECTION SPECIFICATIONS

---

## 00 — Executive Summary (`00-executive-summary.md`)

- Project name, one-paragraph purpose, and core value proposition.
- Target users / personas.
- Key business problem it solves.
- Current maturity stage (MVP, beta, production, legacy).

---

## 01 — Architecture (`01-architecture.md`)

### 1.1 C4 Model → `diagrams/workspace.dsl`

All four C4 levels are defined in a single Structurizr DSL workspace file. This means one unified model that renders System Context, Container, Component, and Code-level views — all staying consistent automatically.

Reference the file from prose: `See [C4 Architecture Model](diagrams/workspace.dsl)`.

The `workspace.dsl` file must follow this structure:

```
workspace "[Project Name]" "[One-line description]" {

    model {
        // 1. Define all external actors (people and external systems)
        user = person "User" "Description of primary user persona"
        admin = person "Admin" "Description of admin persona"
        externalSystem = softwareSystem "External System Name" "What it does" "External"

        // 2. Define the main software system
        system = softwareSystem "[Project Name]" "Core value proposition" {

            // 3. Define containers (deployable units)
            webApp = container "Web Application" "Serves the frontend" "React / Next.js / etc." "Web Browser"
            apiServer = container "API Server" "Handles business logic and API requests" "Node.js / Python / etc."
            database = container "Database" "Stores persistent data" "PostgreSQL / MySQL / etc." "Database"
            cache = container "Cache" "Session and query caching" "Redis" "Database"
            worker = container "Background Worker" "Processes async jobs" "Sidekiq / Celery / etc."
            // ... add all containers found in the codebase

            // 4. Define components inside each major container
            apiServer {
                authController = component "Auth Controller" "Handles authentication and authorization" "Express middleware / etc."
                userService = component "User Service" "User CRUD and business logic" "Service class"
                orderService = component "Order Service" "Order processing and state management" "Service class"
                repository = component "Repository Layer" "Data access abstraction" "Repository pattern"
                // ... add all components found in the codebase
            }
        }

        // 5. Define all relationships
        user -> webApp "Uses" "HTTPS"
        webApp -> apiServer "Makes API calls to" "JSON/HTTPS"
        apiServer -> database "Reads from and writes to" "SQL/TCP"
        apiServer -> cache "Caches data in" "Redis protocol"
        apiServer -> externalSystem "Sends requests to" "HTTPS"
        worker -> database "Reads from and writes to" "SQL/TCP"

        // Component-level relationships
        authController -> userService "Validates users via"
        userService -> repository "Persists data via"
        repository -> database "Queries"
        // ... add all relationships found in the codebase
    }

    views {
        // Level 1: System Context — shows the system + all external actors
        systemContext system "SystemContext" {
            include *
            autoLayout
        }

        // Level 2: Container — shows internal containers
        container system "Containers" {
            include *
            autoLayout
        }

        // Level 3: Component — one view per major container
        component apiServer "APIComponents" {
            include *
            autoLayout
        }
        // Repeat for other containers with significant internal structure

        // Level 4: Code — class diagrams for critical modules (use 3-5)
        // Note: Structurizr DSL handles L1-L3 natively. For L4 code-level
        // class diagrams, create separate Mermaid files:
        //   diagrams/class-[module-name].mermaid

        // Styling
        styles {
            element "Person" {
                shape Person
                background #08427B
                color #ffffff
            }
            element "Software System" {
                background #1168BD
                color #ffffff
            }
            element "External" {
                background #999999
                color #ffffff
            }
            element "Container" {
                background #438DD5
                color #ffffff
            }
            element "Component" {
                background #85BBF0
                color #000000
            }
            element "Database" {
                shape Cylinder
            }
            element "Web Browser" {
                shape WebBrowser
            }
        }
    }
}
```

**Requirements for the workspace.dsl:**
- Define every person, external system, container, and component found in the codebase.
- Label every relationship with what flows across it and the protocol/technology.
- Use tags ("External", "Database", "Web Browser") to apply correct shapes and colors.
- Create one `component` view per container that has 3+ internal components.
- For Level 4 code-level diagrams (class/module detail), create separate Mermaid class diagram files: `diagrams/class-[module-name].mermaid` (one per critical module, 3–5 total). Reference these from the prose.

### 1.2 Deployment Diagram → `diagrams/deployment.mermaid`
Infrastructure topology: cloud provider, regions, services, networking, load balancers, DNS.

### 1.3 Sequence Diagrams → one `diagrams/sequence-[flow-name].mermaid` each
Create sequence diagrams for the 5–8 most important user flows showing request/response across components.

### 1.4 Data Flow → `diagrams/data-flow.mermaid`
How data moves through the system from ingestion to storage to presentation.

### 1.5 State Machines → one `diagrams/state-[entity-name].mermaid` each
For every entity that has a lifecycle with defined states and transitions.

---

## 02 — Tech Stack (`02-tech-stack.md`)

For every technology found in the codebase, list in a table:

| Layer | Technology | Version | Purpose |
|-------|-----------|---------|---------|
| Language | | | |
| Framework | | | |
| Database | | | |
| ORM / Data Access | | | |
| Cache | | | |
| Message Broker | | | |
| Auth | | | |
| Frontend | | | |
| CSS / UI | | | |
| Build / Bundler | | | |
| Testing | | | |
| CI/CD | | | |
| Infrastructure | | | |
| Monitoring | | | |
| External APIs | | | |
| Other | | | |

Also document:
- Minimum runtime versions required.
- Environment variables and their purposes (redact secrets, list keys only).
- Required third-party accounts / API keys.

---

## 03 — Data Model (`03-data-model.md`)

### 3.1 Entity Catalog

For every domain entity / model / table:

**Entity: `[Name]`**
- Description: What it represents in the business domain.
- Ownership: Which module / service owns it.
- Lifecycle states (if any).
- Soft delete: yes/no.
- Auditing: created_at, updated_at, created_by, etc.

### 3.2 ER Diagram → `diagrams/er-diagram.mermaid`

Reference the diagram. It must contain all entities with attributes, types, primary keys, foreign keys, unique constraints, relationships with cardinality, and join/pivot tables.

### 3.3 Field-Level Detail

For each entity, a table:

| Field | Type | Nullable | Default | Constraints | Description |
|-------|------|----------|---------|-------------|-------------|

### 3.4 Indexes & Performance Considerations

List all indexes (unique, composite, partial, full-text) found in migrations or schema definitions and their purpose.

---

## 04 — API Contracts (`04-api-contracts.md`)

### 4.1 API Overview
- Base URL pattern, API style (REST, GraphQL, gRPC, WebSocket, hybrid).
- Versioning strategy.
- Authentication mechanism (JWT, API key, OAuth2, session, etc.).
- Rate limiting, throttling, pagination pattern, error response format.

### 4.2 Endpoint Catalog

For every endpoint / query / mutation / subscription / gRPC method:

**`[METHOD] [PATH]`**
- Summary.
- Auth: Required role / permission / scope.
- Request: headers, query params, path params, body schema (types + validation).
- Response: schema per status code (200, 201, 400, 401, 403, 404, 409, 422, 500).
- Example request and response payloads.
- Side effects (emails, events, jobs).

### 4.3 WebSocket / Real-Time Events
Event name, payload schema, direction, connection lifecycle.

### 4.4 External API Integrations
For each third-party API consumed: service name, endpoints called, auth, retry/fallback, data mapping.

---

## 05 — Functional Requirements (`05-functional-requirements.md`)

Number each requirement (FR-001, FR-002, ...). Group by domain area.

| ID | Title | Description | Priority (MoSCoW) | Source File(s) | Dependencies |
|----|-------|-------------|-------------------|----------------|--------------|

---

## 06 — User Stories (`06-user-stories.md`)

Extract every distinct piece of functionality as a user story. Number them US-001, US-002, ...

For each:

### US-XXX: [Title]

**Story:** As a [role], I want to [action], so that [benefit].

**Description:** Detailed explanation including business rules found in code.

**Why:** Business justification.

**Acceptance Criteria:**

```gherkin
Scenario: [Descriptive name]
  Given [precondition]
  When [action]
  Then [expected outcome]
  And [additional outcomes]
```

Provide multiple scenarios per story covering: happy path, validation failures, authorization failures, edge cases, concurrency (if applicable).

**Independent Test:** A self-contained test description (setup → execution → assertion) to verify this story in isolation.

**Edge Cases:** Bullet list of edge cases specific to this story.

---

## 07 — Edge Cases — System-Wide (`07-edge-cases.md`)

Consolidated edge cases spanning multiple features:

| ID | Category | Description | Current Handling | Risk |
|----|----------|-------------|------------------|------|

Categories: data integrity, auth, concurrency, network, input validation, timezone/locale, file handling, rate limiting, caching, state transitions, third-party failures, migration/upgrade.

---

## 08 — Non-Functional Requirements (`08-non-functional-requirements.md`)

Subsections for each with evidence from code:

- **8.1 Performance**: Response time targets, throughput, DB query constraints, caching/indexing/pagination evidence.
- **8.2 Scalability**: Horizontal/vertical patterns, stateless/stateful, sharding/replication.
- **8.3 Security**: Auth details, authorization model (RBAC/ABAC), input sanitization, CORS, encryption, secrets management, OWASP Top 10.
- **8.4 Reliability**: Error handling, retry/circuit breakers, health checks, graceful degradation, backup/DR.
- **8.5 Observability**: Logging strategy, metrics, tracing, alerting.
- **8.6 Accessibility**: WCAG level, ARIA, keyboard nav, screen reader support.
- **8.7 Internationalization**: i18n/l10n support, locales, date/number formatting.

---

## 09 — Design Guidelines (`09-design-guidelines.md`)

### 9.1 UI/UX Patterns
- Design system / component library used.
- Color palette (extract hex values).
- Typography (font families, size scale).
- Spacing / layout system, responsive breakpoints.
- Dark mode / theme support.

### 9.2 Code Design Patterns
- Architectural pattern (MVC, Clean Architecture, Hexagonal, etc.).
- Design patterns (Repository, Factory, Observer, Strategy, etc.).
- Naming conventions (files, variables, functions, classes, routes).
- Error handling and folder structure conventions.

### 9.3 API Design Conventions
- Naming patterns, filtering/sorting/pagination, response envelope patterns.

---

## 10 — Business Rules & Domain Logic (`10-business-rules.md`)

| ID | Rule | Location in Code | Description |
|----|------|-----------------|-------------|
| BR-001 | | | |

Include: pricing, discounts, status transitions, permission checks, computed fields, scheduled logic, notification triggers.

---

## 11 — Background Jobs & Scheduled Tasks (`11-background-jobs.md`)

| Job | Schedule / Trigger | Purpose | Idempotent | Timeout | Retry Policy |
|-----|--------------------|---------|------------|---------|--------------|

---

## 12 — Event & Message Contracts (`12-events-and-messages.md`)

| Event | Producer | Consumer(s) | Payload Schema | Ordering Guarantee | Idempotency |
|-------|----------|-------------|----------------|-------------------|-------------|

---

## 13 — Assumptions (`13-assumptions.md`)

| ID | Assumption | Reasoning | Risk if Wrong |
|----|-----------|-----------|---------------|
| A-001 | | | |

---

## 14 — Success Criteria & Measurable Outcomes (`14-success-criteria.md`)

### 14.1 Success Criteria

| ID | Criterion | How to Verify |
|----|-----------|---------------|
| SC-001 | | |

### 14.2 Measurable Outcomes

| ID | Metric | Target | Measurement Method |
|----|--------|--------|--------------------|
| MO-001 | | | |

Derive from code — what is tracked, logged, or dashboarded.

---

## 15 — Migration & Data Seeding (`15-migration-and-seeding.md`)

- All database migrations in order with description.
- Required seed data.
- Data migration strategies (backfills, transformations).

---

## 16 — Testing Strategy (`16-testing-strategy.md`)

### 16.1 Current Test Coverage
Test types, runner/framework, coverage metrics, test data approach.

### 16.2 Critical Test Scenarios
Top 20 scenarios that must pass for production release.

---

## 17 — Configuration & Feature Flags (`17-configuration-and-feature-flags.md`)

| Config Key | Type | Default | Description | Environment-Specific |
|------------|------|---------|-------------|---------------------|

Include all feature flags, toggles, and A/B test configs.

---

## 18 — Technical Debt (`18-technical-debt.md`)

Extract every TODO, FIXME, HACK, WORKAROUND from the codebase:

| Location | Type | Comment | Severity |
|----------|------|---------|----------|

Also note architectural debt: tight coupling, missing abstractions, outdated dependencies, missing tests.

---

## 19 — Glossary (`19-glossary.md`)

| Term | Definition |
|------|-----------|

Every domain-specific term, acronym, and concept used in the codebase.

---

### INSTRUCTIONS FOR THE AGENT

1. **Scan first, write second.** Read the entire directory tree and every config, migration, model, controller, service, route, test, and middleware file before producing any output.
2. **Be exhaustive.** If there are 47 API endpoints, document all 47. If there are 12 database tables, document all 12.
3. **Multi-repository support.** If the workspace contains multiple repositories, a monorepo with multiple projects, or git submodules, analyze and document **every single repository/project**. No repository may be skipped. Model each repository as a separate system or container in the C4 model. Document tech stack, data model, APIs, and all other sections per repository where they differ, and show cross-repo relationships.
4. **Use every template — no exceptions.** Every file listed in the output structure above must be created. All 20 Markdown files (00 through 19 plus README.md), the `workspace.dsl`, and all required Mermaid diagram files are mandatory. If a section does not apply, still create the file and explicitly state it is not applicable with a brief justification.
5. **Structurizr DSL for C4 diagrams.** The `workspace.dsl` file must contain valid Structurizr DSL syntax. Define one unified model with views for System Context, Container, and Component levels. Do not use Mermaid for C4 diagrams.
6. **Mermaid for all other diagrams.** Sequence, state, ER, data flow, deployment, and class diagrams use `.mermaid` files containing only valid Mermaid syntax (no markdown fences, no prose).
7. **Validate diagram syntax.** Before writing any diagram file, verify its syntax is correct:
   - **Structurizr DSL**: All identifiers must be defined before use, all relationships must reference valid elements, all views must reference valid containers/components, and styling tags must match element tags. Follow the [Structurizr DSL language reference](https://docs.structurizr.com/dsl/language).
   - **Mermaid**: Each `.mermaid` file must contain only valid Mermaid syntax — no markdown fences, no prose, no front-matter. Validate diagram type declarations (`erDiagram`, `sequenceDiagram`, `stateDiagram-v2`, `flowchart`, `classDiagram`, `graph`), proper arrow syntax (`-->`, `-->>`, `-.->`, etc.), and correct quoting of labels containing special characters.
8. **Prose files reference diagrams.** Use relative links like `[C4 Model](diagrams/workspace.dsl)` and `[ER Diagram](diagrams/er-diagram.mermaid)`.
9. **Derive, don't invent.** Every statement must trace to code. If you cannot find evidence, put it in `13-assumptions.md`.
10. **Include file references.** When documenting a feature or rule, note the source file path.
11. **Number everything.** Use the IDs specified (FR-XXX, US-XXX, EC-XXX, BR-XXX, etc.) for traceability.
12. **Write for a new team.** Assume zero context. Define every acronym, explain every convention.
13. **Mark gaps.** Missing validation, error handling, tests — document them explicitly.
14. **Create files in order.** Start with `workspace.dsl` and Mermaid diagrams (they inform everything else), then `00` through `19`, then `README.md` last (so all links are valid).
15. **Validate links.** Every relative link in README.md and prose files must point to a file you actually created.
