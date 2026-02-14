---
agent: agent
description: "Run all four security audits and generate an executive summary"
tools: ['vscode', 'execute', 'read', 'agent', 'edit', 'search', 'web', 'todo']
---

# Full Security Audit

You are the **Security Orchestrator**. Execute all four specialized security audits sequentially and produce individual domain reports plus a consolidated executive summary.

## Before You Begin

Discover the project tech stack by reading configuration files and scanning the directory structure. Identify:
- **Backend**: Framework, language, database, ORM
- **Frontend**: Framework, language, build tool
- **Infrastructure**: CI/CD platform, cloud provider, IaC tool, hosting
- **Integrations**: AI services, payment, analytics, OAuth providers

This information will be passed to each domain audit for tech-stack-specific checks.

## Instructions

Run each audit in order, generating a separate report for each domain. After all audits are complete, consolidate the top findings into an executive summary.

## Step 1: Frontend Security Audit

Act as the **Frontend Security Agent**. Audit all frontend source code for:
- XSS vulnerabilities (dangerous HTML injection APIs, eval, innerHTML)
- Authentication token handling (storage, expiration, logout cleanup)
- Secret exposure (hardcoded keys, client-exposed env variables)
- Dependency vulnerabilities (run package audit command)
- Input validation and sanitization
- Permission guard completeness on create/update/delete operations
- CSP and security headers
- Client-side data exposure (localStorage, console.log, state cleanup)

**Scope**: All frontend source directories

**Output**: `docs/security/frontend-security-audit-{date}.md`

## Step 2: API Security Audit

Act as the **API Security Agent**. Audit the backend API for:
- Authorization on every controller/route (auth attributes, middleware, permission checks)
- Broken Object Level Authorization (BOLA)
- Injection vulnerabilities (SQL injection, NoSQL injection, command injection)
- Secret exposure (hardcoded credentials, config file secrets)
- Input validation (model/DTO validation, mass assignment)
- Error handling and information leakage
- Security configuration (CORS, HTTPS, auth config, API docs)
- Dependency vulnerabilities (run package vulnerability check)
- Rate limiting and abuse prevention
- SSRF prevention

**Scope**: All backend source directories

**Output**: `docs/security/api-security-audit-{date}.md`

## Step 3: Infrastructure Security Audit

Act as the **Infrastructure Security Agent**. Audit infrastructure and DevOps for:
- CI/CD pipeline security (pinned actions, permissions, secret handling)
- Infrastructure as Code (secure parameters, network isolation, encryption)
- Container security (base images, non-root user, secrets in layers)
- Secret exposure across entire repository
- Dependency supply chain (lock files, lifecycle scripts, CVEs)
- Hosting configuration (security headers, route protection)
- Build configuration (source maps, env variable exposure)
- TLS/network configuration

**Scope**: CI/CD workflows, IaC files, Dockerfiles, config files

**Output**: `docs/security/infrastructure-security-audit-{date}.md`

## Step 4: Data Privacy Audit

Act as the **Data Privacy Agent**. Audit the entire application for GDPR/CCPA compliance:
- PII inventory (map all personal data across frontend, backend, database)
- Consent management (cookies, AI features, marketing — granular, withdrawable, timestamped)
- Data minimization (over-collection, over-fetching, unnecessary PII in logs/storage)
- Right to erasure (cascade delete completeness, orphaned PII)
- DSAR capability (data export for a specific user)
- Third-party data flows (AI, OAuth, payment, email, analytics integrations)
- AI privacy (consent gating, PII in prompts, data retention) — if applicable
- Cookie compliance (inventory, classification, consent-gated)
- Data encryption (at rest, in transit, key management)
- Data retention policies

**Scope**: Entire repository

**Output**: `docs/security/data-privacy-audit-{date}.md`

## Step 5: Executive Summary

After completing all four audits, generate a consolidated executive summary at `docs/security/security-executive-summary-{date}.md` with:

```markdown
# Security Executive Summary
**Date**: {current date}
**Scope**: Full Application Security Assessment
**Domains Assessed**: Frontend, API, Infrastructure, Data Privacy

## Overall Risk Rating
{Critical / High / Medium / Low — based on worst domain}

## Risk Heatmap
| Domain | Critical | High | Medium | Low | Info | Overall |
|---|---|---|---|---|---|---|
| Frontend | {count} | {count} | {count} | {count} | {count} | 🔴/🟠/🟡/🟢 |
| API | {count} | {count} | {count} | {count} | {count} | 🔴/🟠/🟡/🟢 |
| Infrastructure | {count} | {count} | {count} | {count} | {count} | 🔴/🟠/🟡/🟢 |
| Data Privacy | {count} | {count} | {count} | {count} | {count} | 🔴/🟠/🟡/🟢 |
| **Total** | **{sum}** | **{sum}** | **{sum}** | **{sum}** | **{sum}** | **{overall}** |

## Top 10 Critical & High Findings
| # | Domain | Finding | Severity | Regulatory Impact | Effort to Fix |
|---|---|---|---|---|---|

## Compliance Summary

### OWASP Top 10 (Frontend + API)
| Category | Frontend | API | Combined |
|---|---|---|---|
| A01: Broken Access Control | ✅/⚠️/❌ | ✅/⚠️/❌ | ... |
| A02: Cryptographic Failures | ... | ... | ... |
| A03: Injection | ... | ... | ... |

### OWASP API Security Top 10
{Summary from API audit}

### GDPR Compliance
| Key Article | Status | Gap Summary |
|---|---|---|
| Art. 5 (Principles) | ✅/⚠️/❌ | ... |
| Art. 15 (DSAR) | ✅/⚠️/❌ | ... |
| Art. 17 (Erasure) | ✅/⚠️/❌ | ... |
| Art. 25 (Privacy by Design) | ✅/⚠️/❌ | ... |
| Art. 32 (Security) | ✅/⚠️/❌ | ... |
| Art. 33 (Breach Notification) | ✅/⚠️/❌ | ... |
| Art. 35 (DPIA) | ✅/⚠️/❌ | ... |

### CIS / SLSA
{Summary from Infrastructure audit}

## Remediation Roadmap
### Immediate (This Sprint)
{Critical findings that must be fixed immediately}

### Short-Term (Next 2-4 Weeks)
{High-severity findings}

### Medium-Term (Next Quarter)
{Medium findings and compliance gaps}

### Long-Term (Ongoing)
{Low findings, process improvements, monitoring}

## Detailed Reports
- [Frontend Security Audit](frontend-security-audit-{date}.md)
- [API Security Audit](api-security-audit-{date}.md)
- [Infrastructure Security Audit](infrastructure-security-audit-{date}.md)
- [Data Privacy Audit](data-privacy-audit-{date}.md)

## Next Steps
1. {Immediate action items}
2. {Schedule follow-up audit in X months}
3. {Process improvements}
```
