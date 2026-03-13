# Security Agents for AI Coding Assistants

A collection of reusable AI security agent instructions and audit prompts for [VS Code Copilot Chat](https://code.visualstudio.com/docs/copilot/overview), [GitHub Copilot](https://github.com/features/copilot), and compatible AI coding assistants.

These agents turn your AI assistant into specialized auditors that can perform comprehensive security assessments and system documentation:

| Agent | Domain | Standards / Output |
|-------|--------|-----------|
| **API Security** | Backend APIs & data access | OWASP API Top 10, CWE, NIST |
| **Frontend Security** | Client-side web applications | OWASP Top 10, CSP, CWE |
| **Infrastructure Security** | CI/CD, IaC, containers, supply chain | CIS Benchmarks, SLSA, NIST |
| **Data Privacy** | GDPR, CCPA, consent, PII handling | GDPR, CCPA/CPRA, ISO 27701 |
| **System Documentation** | Architecture, data model, APIs, requirements | C4 (Structurizr DSL), Mermaid, Markdown |

Plus a **Full Security Audit** orchestrator that runs all four security audits sequentially and produces an executive summary.

## Quick Start

### 1. Copy to Your Repository

Copy the `.github/` folder into your project root:

```bash
# Clone this repo
git clone https://github.com/YOUR_ORG/security-agents-ai.git

# Copy agents into your project
cp -r security-agents-ai/.github/instructions/ YOUR_PROJECT/.github/instructions/
cp -r security-agents-ai/.github/prompts/ YOUR_PROJECT/.github/prompts/
```

### 2. Run an Audit

In VS Code with GitHub Copilot, open the Copilot Chat window and type:

| Command | Description |
|---------|-------------|
| `/security-api-audit` | Backend API audit |
| `/security-frontend-audit` | Frontend audit |
| `/security-infrastructure-audit` | Infrastructure & DevOps audit |
| `/security-data-privacy-audit` | GDPR/CCPA privacy audit |
| `/security-full-audit` | All four audits + executive summary |
| `/system-documentation` | Full system specification from codebase |

The agent will auto-detect your tech stack and begin.

> **Alternative:** You can also run audits via the Command Palette (`Ctrl+Shift+P` / `Cmd+Shift+P`) → type **"Run Prompt"** → select the desired audit.

### 3. Review Reports

Reports are generated in `docs/security/` with timestamps:
```
docs/security/
├── api-security-audit-2026-02-14.md
├── frontend-security-audit-2026-02-14.md
├── infrastructure-security-audit-2026-02-14.md
├── data-privacy-audit-2026-02-14.md
└── security-executive-summary-2026-02-14.md
```

## How It Works

### Instructions (Always-On Context)

Files in `.github/instructions/` are **automatically loaded** by GitHub Copilot when you work on matching files. They provide persistent security awareness:

| File | Applies To | Effect |
|------|-----------|--------|
| `security-api.instructions.md` | `backend/**/*.{cs,java,py,go,rb,js,ts}` | Guides AI to flag security issues in backend code |
| `security-frontend.instructions.md` | `**/*.{ts,tsx,js,jsx,vue,svelte,css,html}` | Guides AI to flag XSS, auth, and client-side issues |
| `security-infrastructure.instructions.md` | `**/*.{yml,yaml,json,bicep,tf,...}` | Guides AI to flag IaC and pipeline issues |
| `security-data-privacy.instructions.md` | `**` | Guides AI to flag privacy/GDPR issues everywhere |
| `system-documentation.instructions.md` | `**` | Guides AI to produce system specs from code |

### Prompts (On-Demand Audits)

Files in `.github/prompts/` are **on-demand audit workflows** you trigger manually. Each prompt:
1. Auto-detects your project's tech stack
2. Performs systematic security checks
3. Generates a structured Markdown report with findings, severity, and remediation

## Supported Tech Stacks

The agents are designed to be **tech-stack agnostic**. They auto-detect and adapt to:

### Backend
- ASP.NET Core / C#
- Spring Boot / Java
- Django / FastAPI / Flask / Python
- Express / NestJS / Node.js
- Ruby on Rails
- Go (net/http, Gin, Echo)

### Frontend
- React / Next.js
- Vue / Nuxt
- Angular
- Svelte / SvelteKit
- Vanilla JavaScript/TypeScript

### Infrastructure
- GitHub Actions, GitLab CI, Azure DevOps, Jenkins
- Docker, Kubernetes
- Terraform, Bicep, CloudFormation, Pulumi
- Azure, AWS, GCP

### Databases
- SQL Server, PostgreSQL, MySQL
- MongoDB, Cosmos DB, DynamoDB
- Redis, Elasticsearch

## Customization

### Adjusting File Patterns

Edit the `applyTo` frontmatter in instruction files to match your project structure:

```yaml
---
# Default (covers most projects)
applyTo: "backend/**/*.{cs,java,py,go,rb,js,ts}"

# Customized for a Python Django project
applyTo: "server/**/*.py"
---
```

### Adding Project-Specific Rules

You can extend any instruction file with project-specific security rules. Add a section at the end:

```markdown
## Project-Specific Rules

### Custom Authorization Pattern
- All views must use the `@login_required` decorator
- Admin endpoints must also use `@admin_required`
- API endpoints use `IsAuthenticated` permission class
```

### Changing Report Output Location

Edit the prompt files to change the output path:

```markdown
## Output
Generate a report at `security-reports/api-audit-{date}.md`
```

## File Structure

```
.github/
├── instructions/                              # Always-on security context
│   ├── security-api.instructions.md           # API/backend security rules
│   ├── security-data-privacy.instructions.md  # GDPR/CCPA privacy rules
│   ├── security-frontend.instructions.md      # Frontend/client security rules
│   ├── security-infrastructure.instructions.md # DevOps/infra security rules
│   └── system-documentation.instructions.md   # System documentation templates
└── prompts/                                   # On-demand audit workflows
    ├── security-api-audit.prompt.md           # Full API security audit
    ├── security-data-privacy-audit.prompt.md  # Full privacy/GDPR audit
    ├── security-frontend-audit.prompt.md      # Full frontend security audit
    ├── security-infrastructure-audit.prompt.md # Full infrastructure audit
    ├── security-full-audit.prompt.md          # Orchestrator: runs all 4 + summary
    └── system-documentation.prompt.md         # Full system specification generator    └── system-documentation.prompt.md         # Full system specification generator```

## System Documentation Agent

The **System Documentation** agent reverse-engineers your codebase into a comprehensive, implementation-ready specification. It produces:

- **20 structured Markdown files** covering architecture, data model, API contracts, functional requirements, user stories, business rules, and more
- **C4 architecture diagrams** in Structurizr DSL (System Context, Container, Component views)
- **Mermaid diagrams** for ER diagrams, sequence flows, state machines, data flow, and deployment topology

### Key Features

- **Multi-repository support**: Automatically discovers and documents all repositories in the workspace (monorepo, multi-repo, git submodules)
- **Diagram validation**: Ensures all Structurizr DSL and Mermaid syntax is valid before output
- **Exhaustive coverage**: Documents every API endpoint, database table, entity, and business rule found in code
- **Template completeness**: All 20+ specification files are always generated — nothing is skipped

### Running

```
/system-documentation
```

### Output

Reports are generated in `docs/spec/`:
```
docs/spec/
├── README.md
├── 00-executive-summary.md  through  19-glossary.md
└── diagrams/
    ├── workspace.dsl              # C4 architecture (Structurizr DSL)
    ├── er-diagram.mermaid         # Entity-relationship diagram
    ├── deployment.mermaid         # Infrastructure topology
    ├── data-flow.mermaid          # Data movement diagram
    ├── state-*.mermaid            # State machines per entity
    └── sequence-*.mermaid         # Sequence diagrams per flow
```

## Severity Levels

All agents use a consistent severity scale:

| Level | Icon | Meaning | Action Required |
|-------|------|---------|-----------------|
| Critical | 🔴 | Exploitable vulnerability or regulatory violation | Fix immediately |
| High | 🟠 | Significant security weakness | Fix this sprint |
| Medium | 🟡 | Security improvement needed | Fix next 2-4 weeks |
| Low | 🔵 | Minor hardening opportunity | Fix next quarter |
| Informational | ⚪ | Best practice suggestion | Consider implementing |

## Standards Coverage

| Standard | Agent(s) |
|----------|----------|
| OWASP Top 10 (2021) | Frontend, API |
| OWASP API Security Top 10 (2023) | API |
| OWASP Privacy Risks Top 10 | Data Privacy |
| CWE/SANS Top 25 | Frontend, API |
| GDPR (EU 2016/679) | Data Privacy |
| CCPA/CPRA | Data Privacy |
| CIS Benchmarks | Infrastructure |
| SLSA Framework | Infrastructure |
| NIST SP 800-53 | API, Infrastructure |
| NIST SP 800-190 (Containers) | Infrastructure |
| ISO 27701 | Data Privacy |
| PCI DSS | Data Privacy (payment flows) |

## Contributing

1. Fork this repository
2. Add or improve agent instructions/prompts
3. Test with a real project to verify detection quality
4. Submit a pull request with before/after examples

## License

MIT License — use freely in personal and commercial projects.
