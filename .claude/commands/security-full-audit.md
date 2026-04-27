# Full Security Audit

You are the **Security Orchestrator**. Execute all four specialized security audits sequentially, perform threat modeling, and produce individual domain reports plus a consolidated executive summary.

## Scope

$ARGUMENTS

If no scope is specified, audit the entire repository.

## Before You Begin

Discover the project tech stack by reading configuration files and scanning the directory structure. Identify:
- **Backend**: Framework, language, database, ORM
- **Frontend**: Framework, language, build tool
- **Infrastructure**: CI/CD platform, cloud provider, IaC tool, hosting
- **Integrations**: AI services, payment, analytics, OAuth providers

This information will be passed to each domain audit for tech-stack-specific checks.

## Global Audit Rules

### Anti-Hallucination Rules
- Do NOT fabricate CVE numbers. Use CWE identifiers for vulnerability classes.
- Only cite CVEs returned by actual audit command output.
- Mark uncertain findings as "REQUIRES VERIFICATION."
- Distinguish "vulnerability" (exploitable now) from "weakness" (could become exploitable).

### Severity Rating (applies to all domains)
- **CRITICAL**: Remotely exploitable, leads to RCE, data breach, or complete tenant isolation bypass. CVSS 9.0-10.0.
- **HIGH**: Requires authentication but leads to significant data access, privilege escalation, or cross-tenant access. CVSS 7.0-8.9.
- **MEDIUM**: Requires specific conditions, defense-in-depth gap. CVSS 4.0-6.9.
- **LOW**: Theoretical, requires insider access or chained exploits. CVSS 0.1-3.9.
- **INFORMATIONAL**: Hardening suggestion, no current exploitability.

### Analysis Methodology
For every finding, complete: Detection -> Reachability Analysis -> Exploitability Assessment.
Only report findings that survive all three phases.

## Instructions

Run each audit in order, generating a separate report for each domain. After all audits are complete, perform threat modeling and consolidate into an executive summary.

## Step 1: Frontend Security Audit

Act as the **Frontend Security Agent**. Audit all frontend source code for:
- XSS vulnerabilities (dangerouslySetInnerHTML, eval, innerHTML, DOM clobbering, mutation XSS)
- Trusted Types API compliance
- Authentication token handling (BFF pattern, storage, expiration, logout cleanup)
- Secret exposure (hardcoded keys, client-exposed env variables, source maps)
- Dependency vulnerabilities and supply chain risks
- Input validation and sanitization
- Permission guard completeness
- CSP Level 3 compliance and security headers (HSTS, Permissions-Policy, COOP/COEP/CORP)
- Modern CSRF prevention (SameSite cookies, Sec-Fetch-Site)
- Client-side prototype pollution
- Client-side data exposure
- Service Worker and Web Worker security
- WebSocket security
- React Server Components / Next.js App Router security (if applicable)
- CSS injection vectors
- postMessage cross-origin communication security
- Third-party script sandboxing and SRI

**Output**: `docs/security/frontend-security-audit-{date}.md`

## Step 2: API Security Audit

Act as the **API Security Agent**. Audit the backend API for:
- Authorization on every controller/route (auth attributes, middleware, permission checks, BOLA)
- Injection vulnerabilities (SQL, NoSQL, command injection, SSTI, LDAP, XPath)
- Insecure deserialization
- JWT security (algorithm confusion, claim validation, kid injection)
- Cryptographic failures (weak algorithms, insecure randomness, key management)
- Secret exposure
- Input validation (model/DTO validation, mass assignment, file upload security)
- Error handling and information leakage
- Security configuration (CORS, HTTPS, auth config, API docs)
- Dependency vulnerabilities
- Rate limiting and abuse prevention
- SSRF prevention
- Race conditions / TOCTOU
- Business logic abuse
- GraphQL/gRPC security (if applicable)
- API inventory and shadow API detection
- Logging and monitoring

**Output**: `docs/security/api-security-audit-{date}.md`

## Step 3: Infrastructure Security Audit

Act as the **Infrastructure Security Agent**. Audit infrastructure and DevOps for:
- CI/CD pipeline security (SHA-pinned actions, least-privilege permissions, OIDC, script injection)
- CI/CD pipeline poisoning prevention
- Infrastructure as Code security
- SLSA supply chain compliance
- SBOM generation and verification
- Artifact signing
- Container security (distroless images, non-root, no secrets in layers)
- Kubernetes security (if applicable)
- Secret exposure across entire repository
- Dependency supply chain
- Hosting configuration
- Build configuration
- Network & TLS configuration
- SAST/DAST integration
- OpenSSF Scorecard assessment

**Output**: `docs/security/infrastructure-security-audit-{date}.md`

## Step 4: Data Privacy Audit

Act as the **Data Privacy Agent**. Audit the entire application for regulatory compliance:
- PII inventory
- Consent management
- Data minimization
- Right to erasure
- DSAR capability
- Third-party data flows
- AI privacy (if applicable)
- Cookie compliance
- Data encryption
- Data retention policies
- Cross-border data transfers
- Children's privacy (if applicable)
- US state privacy law compliance
- NIS2 compliance (if applicable)
- Privacy by design assessment
- Breach notification readiness

**Output**: `docs/security/data-privacy-audit-{date}.md`

## Step 5: Threat Modeling

### STRIDE Analysis
For each major trust boundary:
| Threat | Question |
|--------|----------|
| **S**poofing | Can an attacker impersonate a legitimate user or service? |
| **T**ampering | Can data be modified in transit or at rest without detection? |
| **R**epudiation | Can a user deny performing an action? |
| **I**nformation Disclosure | Can sensitive data leak to unauthorized parties? |
| **D**enial of Service | Can the service be made unavailable? |
| **E**levation of Privilege | Can a user gain higher privileges than intended? |

### Trust Boundaries to Analyze
1. Internet <-> Frontend
2. Frontend <-> Backend API
3. Backend API <-> Database
4. Backend API <-> File Storage
5. Backend API <-> External Services
6. Tenant A <-> Tenant B (if multi-tenant)
7. Regular User <-> Admin User
8. CI/CD Pipeline <-> Production Environment

### LINDDUN Privacy Threat Analysis
| Threat | Check |
|--------|-------|
| **L**inkability | Can separate user actions be correlated? |
| **I**dentifiability | Is PII exposed in URLs, logs, or API responses? |
| **N**on-repudiation | Are audit logs creating surveillance concerns? |
| **D**etectability | Do APIs reveal resource existence (404 vs 403)? |
| **D**isclosure | Cross-tenant data leaks? Verbose errors? |
| **U**nawareness | Clear notice for all data collection? |
| **N**on-compliance | Regulatory requirements met? |

## Step 6: NIST SSDF Compliance Assessment

Assess alignment with NIST Secure Software Development Framework (SP 800-218):

| Practice Group | Practice | Status |
|---|---|---|
| **PO: Prepare** | Security requirements defined | ... |
| **PO: Prepare** | Security toolchain implemented (SAST/DAST/SCA) | ... |
| **PS: Protect** | Code protected from unauthorized access | ... |
| **PS: Protect** | Software integrity verification (signing, SLSA) | ... |
| **PW: Produce** | Threat model exists | ... |
| **PW: Produce** | Code reviewed for security | ... |
| **PW: Produce** | Security testing in place | ... |
| **RV: Respond** | Vulnerability identification process | ... |
| **RV: Respond** | Vulnerability remediation SLAs | ... |

## Step 7: Executive Summary

After completing all audits and threat modeling, generate a consolidated executive summary at `docs/security/security-executive-summary-{date}.md` where `{date}` is today's date in YYYY-MM-DD format.

Include:

```markdown
# Security Executive Summary
**Date**: {current date}
**Scope**: Full Application Security Assessment
**Domains Assessed**: Frontend, API, Infrastructure, Data Privacy
**Threat Modeling**: STRIDE, LINDDUN
**Compliance Frameworks**: OWASP Top 10, OWASP API Top 10, GDPR, CCPA/CPRA, EU AI Act, NIS2, CIS Benchmarks, SLSA, NIST SSDF
**Auditor**: Security Orchestrator (Claude Code)

## Overall Risk Rating
{Critical / High / Medium / Low -- based on worst domain}

## Risk Heatmap
| Domain | Critical | High | Medium | Low | Info | Overall |
|---|---|---|---|---|---|---|
| Frontend | {count} | ... | ... | ... | ... | ... |
| API | {count} | ... | ... | ... | ... | ... |
| Infrastructure | {count} | ... | ... | ... | ... | ... |
| Data Privacy | {count} | ... | ... | ... | ... | ... |
| **Total** | **{sum}** | ... | ... | ... | ... | **{overall}** |

## Top 10 Critical & High Findings
| # | Domain | Finding ID | Finding | Severity | Confidence | Effort |
|---|---|---|---|---|---|---|

## Threat Model Summary
## Compliance Summary (OWASP, GDPR, SLSA, NIST SSDF, CIS)

## Remediation Roadmap
### Immediate (This Sprint)
### Short-Term (Next 2-4 Weeks)
### Medium-Term (Next Quarter)
### Long-Term (Ongoing)

## Detailed Reports
- [Frontend Security Audit](frontend-security-audit-{date}.md)
- [API Security Audit](api-security-audit-{date}.md)
- [Infrastructure Security Audit](infrastructure-security-audit-{date}.md)
- [Data Privacy Audit](data-privacy-audit-{date}.md)
```
