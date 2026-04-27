# Security & Documentation Agents for AI Coding Assistants

Reusable AI agent instructions and audit prompts for [GitHub Copilot](https://github.com/features/copilot), [Claude Code](https://docs.anthropic.com/en/docs/claude-code), and compatible AI coding assistants. These agents turn your assistant into specialized security auditors and documentation generators -- usable interactively in VS Code or automated via GitHub Actions.

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

### Option A: GitHub Copilot (VS Code)

#### 1. Copy to your project

```bash
cp -r .github/instructions/ YOUR_PROJECT/.github/instructions/
cp -r .github/prompts/ YOUR_PROJECT/.github/prompts/
```

#### 2. Run an audit

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

### Option B: Claude Code (VS Code, CLI, or Web)

#### 1. Copy to your project

```bash
cp -r .claude/ YOUR_PROJECT/.claude/
```

#### 2. Run an audit

From Claude Code (CLI, VS Code extension, or claude.ai/code), use the slash commands:

| Command | Description |
|---------|-------------|
| `/project:security-api-audit` | Backend API audit |
| `/project:security-frontend-audit` | Frontend audit |
| `/project:security-infrastructure-audit` | Infrastructure & DevOps audit |
| `/project:security-data-privacy-audit` | GDPR/CCPA privacy audit |
| `/project:security-full-audit` | All four audits + executive summary |
| `/project:system-documentation` | Full system specification from codebase |

All commands accept an optional scope argument, e.g. `/project:security-api-audit src/api/`.

### Option C: GitHub Actions (Automated)

#### 1. Copy workflows to your project

```bash
cp -r .github/workflows/ YOUR_PROJECT/.github/workflows/
cp -r .claude/ YOUR_PROJECT/.claude/
```

#### 2. Set up secrets

Add `ANTHROPIC_API_KEY` to your repository secrets (Settings > Secrets and variables > Actions).

#### 3. Available workflows

| Workflow | Trigger | Description |
|----------|---------|-------------|
| **Scheduled Security Audit** | Weekly (Monday 06:00 UTC) or manual | Runs a full audit on schedule, creates a PR with reports |
| **PR Security Review** | On pull request | Reviews changed files for security issues, comments on PR |
| **Manual Security Audit** | workflow_dispatch | Pick any audit type, optional scope, optional issue creation |

**Scheduled audit** runs automatically every Monday. Override via workflow_dispatch to run any specific audit type on demand.

**PR security review** automatically reviews every PR for security issues in changed files. It detects which domains are affected (backend, frontend, infrastructure) and applies targeted checks. Trigger a review on any PR by commenting `/security-review`.

**Manual audit** lets you pick the exact audit type, specify a scope, and optionally create GitHub issues for critical/high findings.

### 3. Review reports

Security reports go to `docs/security/`, system documentation to `docs/spec/`.

## File Structure & Last Updated

### Instructions (always-on context -- Copilot)

Automatically loaded by Copilot when you work on matching files.

| File | Applies to | Last updated |
|------|-----------|-------------|
| `security-api.instructions.md` | `backend/**/*.{cs,java,py,go,rb,js,ts}` | 2026-04-22 |
| `security-frontend.instructions.md` | `**/*.{ts,tsx,js,jsx,vue,svelte,css,html}` | 2026-04-22 |
| `security-infrastructure.instructions.md` | `**/*.{yml,yaml,json,bicep,tf,...}` | 2026-04-22 |
| `security-data-privacy.instructions.md` | `**` | 2026-04-22 |
| `system-documentation.instructions.md` | `**` | 2026-04-22 |

### Prompts (on-demand audits -- Copilot)

Triggered manually from Copilot Chat.

| File | Last updated |
|------|-------------|
| `security-api-audit.prompt.md` | 2026-04-22 |
| `security-frontend-audit.prompt.md` | 2026-04-22 |
| `security-infrastructure-audit.prompt.md` | 2026-04-22 |
| `security-data-privacy-audit.prompt.md` | 2026-04-22 |
| `security-full-audit.prompt.md` | 2026-04-22 |
| `system-documentation.prompt.md` | 2026-04-22 |

### Custom Slash Commands (Claude Code)

Project-level commands for Claude Code. Invoke with `/project:<command-name>`.

| File | Last updated |
|------|-------------|
| `security-api-audit.md` | 2026-04-23 |
| `security-frontend-audit.md` | 2026-04-23 |
| `security-infrastructure-audit.md` | 2026-04-23 |
| `security-data-privacy-audit.md` | 2026-04-23 |
| `security-full-audit.md` | 2026-04-23 |
| `system-documentation.md` | 2026-04-23 |

### GitHub Actions Workflows

| Workflow | Trigger | Last updated |
|----------|---------|-------------|
| `security-audit-scheduled.yml` | Weekly schedule + manual | 2026-04-23 |
| `security-pr-check.yml` | Pull requests | 2026-04-23 |
| `security-audit-manual.yml` | Manual (workflow_dispatch) | 2026-04-23 |

## Customization

**Change file patterns** -- edit the `applyTo` frontmatter in instruction files:

```yaml
---
applyTo: "server/**/*.py"  # default is "backend/**/*.{cs,java,py,go,rb,js,ts}"
---
```

**Add project-specific rules** -- append to any instruction file or Claude Code command:

```markdown
## Project-Specific Rules
- All views must use the `@login_required` decorator
- Admin endpoints must also use `@admin_required`
```

**Change report output location** -- edit the output path in prompt files or slash commands.

**Change scheduled audit frequency** -- edit the cron expression in `security-audit-scheduled.yml`:

```yaml
schedule:
  - cron: '0 6 * * 1'  # Weekly on Monday at 06:00 UTC
  # Examples:
  # '0 6 * * *'     # Daily at 06:00 UTC
  # '0 6 1 * *'     # Monthly on the 1st at 06:00 UTC
  # '0 6 * * 1,4'   # Monday and Thursday at 06:00 UTC
```

## Severity Levels

| Level | Meaning |
|-------|---------|
| Critical | Exploitable vulnerability or regulatory violation -- fix immediately |
| High | Significant security weakness -- fix this sprint |
| Medium | Security improvement needed -- fix next 2-4 weeks |
| Low | Minor hardening opportunity -- fix next quarter |
| Informational | Best practice suggestion -- consider implementing |

## Supported Tech Stacks

The agents auto-detect and adapt to your stack: ASP.NET Core, Spring Boot, Django/FastAPI/Flask, Express/NestJS, Rails, Go, React/Next.js, Vue/Nuxt, Angular, Svelte, GitHub Actions, GitLab CI, Azure DevOps, Docker, Kubernetes, Terraform, Bicep, CloudFormation, and all major cloud providers and databases.

## License

MIT License -- use freely in personal and commercial projects.
