# Security & Documentation Agents for AI Coding Assistants

Reusable AI agent instructions and audit prompts for [GitHub Copilot](https://github.com/features/copilot) and compatible AI coding assistants. These agents turn your assistant into specialized security auditors and documentation generators.

## Agents

| Agent | What it does | Standards |
|-------|-------------|-----------|
| **API Security** | Audits backend APIs and data access | OWASP API Top 10, CWE, NIST |
| **Frontend Security** | Audits client-side web apps | OWASP Top 10, CSP, CWE |
| **Infrastructure Security** | Audits CI/CD, IaC, containers, supply chain | CIS Benchmarks, SLSA, NIST |
| **Data Privacy** | Audits GDPR, CCPA, consent, PII handling | GDPR, CCPA/CPRA, ISO 27701 |
| **Full Security Audit** | Runs all four audits + executive summary | All of the above |
| **System Documentation** | Generates full system spec from codebase | C4 (Structurizr DSL), Mermaid |

## Quick Start

### 1. Copy to your project

```bash
cp -r .github/instructions/ YOUR_PROJECT/.github/instructions/
cp -r .github/prompts/ YOUR_PROJECT/.github/prompts/
```

### 2. Run an audit

Open Copilot Chat in VS Code and type one of these commands:

| Command | Description |
|---------|-------------|
| `/security-api-audit` | Backend API audit |
| `/security-frontend-audit` | Frontend audit |
| `/security-infrastructure-audit` | Infrastructure & DevOps audit |
| `/security-data-privacy-audit` | GDPR/CCPA privacy audit |
| `/security-full-audit` | All four audits + executive summary |
| `/system-documentation` | Full system specification from codebase |

You can also run audits via the Command Palette (`Ctrl+Shift+P` / `Cmd+Shift+P`) > **Run Prompt**.

### 3. Review reports

Security reports go to `docs/security/`, system documentation to `docs/spec/`.

## File Structure & Last Updated

### Instructions (always-on context)

Automatically loaded by Copilot when you work on matching files.

| File | Applies to | Last updated |
|------|-----------|-------------|
| `security-api.instructions.md` | `backend/**/*.{cs,java,py,go,rb,js,ts}` | 2026-04-22 |
| `security-frontend.instructions.md` | `**/*.{ts,tsx,js,jsx,vue,svelte,css,html}` | 2026-04-22 |
| `security-infrastructure.instructions.md` | `**/*.{yml,yaml,json,bicep,tf,...}` | 2026-04-22 |
| `security-data-privacy.instructions.md` | `**` | 2026-04-22 |
| `system-documentation.instructions.md` | `**` | 2026-04-22 |

### Prompts (on-demand audits)

Triggered manually from Copilot Chat. Each prompt auto-detects your tech stack, runs systematic checks, and generates a Markdown report.

| File | Last updated |
|------|-------------|
| `security-api-audit.prompt.md` | 2026-04-22 |
| `security-frontend-audit.prompt.md` | 2026-04-22 |
| `security-infrastructure-audit.prompt.md` | 2026-04-22 |
| `security-data-privacy-audit.prompt.md` | 2026-04-22 |
| `security-full-audit.prompt.md` | 2026-04-22 |
| `system-documentation.prompt.md` | 2026-04-22 |

## Customization

**Change file patterns** -- edit the `applyTo` frontmatter in instruction files:

```yaml
---
applyTo: "server/**/*.py"  # default is "backend/**/*.{cs,java,py,go,rb,js,ts}"
---
```

**Add project-specific rules** -- append to any instruction file:

```markdown
## Project-Specific Rules
- All views must use the `@login_required` decorator
- Admin endpoints must also use `@admin_required`
```

**Change report output location** -- edit the output path in prompt files.

## Severity Levels

| Level | Icon | Meaning |
|-------|------|---------|
| Critical | Red | Exploitable vulnerability or regulatory violation -- fix immediately |
| High | Orange | Significant security weakness -- fix this sprint |
| Medium | Yellow | Security improvement needed -- fix next 2-4 weeks |
| Low | Blue | Minor hardening opportunity -- fix next quarter |
| Informational | White | Best practice suggestion -- consider implementing |

## Supported Tech Stacks

The agents auto-detect and adapt to your stack: ASP.NET Core, Spring Boot, Django/FastAPI/Flask, Express/NestJS, Rails, Go, React/Next.js, Vue/Nuxt, Angular, Svelte, GitHub Actions, GitLab CI, Azure DevOps, Docker, Kubernetes, Terraform, Bicep, CloudFormation, and all major cloud providers and databases.

## License

MIT License -- use freely in personal and commercial projects.
