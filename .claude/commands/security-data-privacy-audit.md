# Data Privacy Audit

You are the **Data Privacy Agent**. Perform a comprehensive privacy and regulatory compliance audit of the entire application stack in this repository.

## Scope

$ARGUMENTS

If no scope is specified, audit the entire repository.

## Before You Begin

Discover the project structure by scanning configuration files and directory structure. Identify:
- **Backend framework and language** for entity/model discovery
- **Database technology** (SQL, NoSQL, hybrid)
- **Frontend framework** for consent flows and PII handling
- **Third-party integrations** (AI services, payment processors, analytics, OAuth providers)
- **Cloud provider** for data residency assessment
- **Applicable jurisdictions** based on user base and business location

Adapt all audit steps below to the detected tech stack.

## Anti-Hallucination Rules

- Do NOT fabricate regulation article numbers. Verify references are accurate.
- Mark uncertain findings as "REQUIRES VERIFICATION" when regulatory interpretation depends on business context.
- Distinguish between "non-compliance" (clear violation) and "compliance gap" (missing documentation or process).

## Audit Steps

### 1. PII Inventory
Map ALL personally identifiable information across the application:
- Search backend entities/models for PII fields (names, emails, phone numbers, addresses, government IDs, payment info, IP addresses, device fingerprints, etc.)
- Search frontend for PII display and collection points
- Search client-side storage (localStorage, sessionStorage, cookies, IndexedDB) for PII
- Search application logs for PII leakage
- Create a complete data map: what PII, where collected, where stored, who accesses, retention period, lawful basis

### 2. Consent Management Audit
- Cookie consent banner -- verify granular consent categories (necessary, functional, analytics, marketing)
- Verify accept/reject options have equal prominence (no dark patterns)
- Feature-specific consent (AI features, analytics, tracking) -- verify captured before usage
- Marketing consent -- verify opt-in (not opt-out) pattern
- Check consent withdrawal mechanisms
- Verify consent records stored with timestamp, version, and choices
- Check for pre-checked consent boxes (GDPR violation)
- Verify no cookies set before consent (except strictly necessary)
- **Google Consent Mode v2**: Check `ad_user_data` and `ad_personalization` signals
- **Global Privacy Control (GPC)**: Verify GPC signal detection and honoring (required by CCPA/CPRA)

### 3. Data Minimization Review
- Are only necessary fields collected?
- Do API responses return only needed PII? (use DTOs, not entity serialization)
- Do application logs contain PII?
- Does client-side storage contain unnecessary PII?

### 4. Right to Erasure (GDPR Art. 17)
- Trace user deletion through all tables/collections
- Check for orphaned PII after entity deletion
- Verify soft-delete vs. hard-delete strategy for PII
- Check backup/archive retention of deleted user PII
- Verify deletion cascades to third-party processors

### 5. DSAR Capability
- Can all data for a specific user be exported?
- Is export in portable format (JSON/CSV)?
- Does export include all data sources?
- Can DSAR be fulfilled within 30 days (GDPR) / 45 days (CCPA)?

### 6. Third-Party Data Flow Analysis
- **AI services**: What data sent in prompts? DPA in place?
- **OAuth providers**: What user data shared? Scopes minimized?
- **Payment processors**: PCI DSS compliance?
- **Analytics**: IP anonymization? Cookie-less tracking?
- Document each third-party with: data categories shared, DPA status, data region

### 7. AI Privacy Compliance (if applicable)
- Consent obtained before AI processing?
- PII minimized in AI prompts?
- Output filtering for PII patterns?
- AI provider data retention opt-out configured?
- Cross-user data isolation verified?
- **EU AI Act**: Risk tier classification
- For high-risk AI: conformity assessment, transparency, human oversight

### 8. Cookie Compliance
- Inventory all cookies
- Classify each: Strictly Necessary, Functional, Analytics, Marketing
- Verify non-essential cookies only set AFTER consent
- Test that rejected consent prevents cookie setting

### 9. Data Encryption Review
- PII encrypted at rest?
- PII encrypted in transit (TLS 1.2+)?
- Field-level encryption for highly sensitive data?
- Key management practices reviewed

### 10. Data Retention Policy Review
- Retention periods defined per data category?
- Automated purge/archival processes?
- Backup retention and PII implications?
- Legal hold integration?

### 11. Cross-Border Data Transfer Compliance
- Map all cross-border data flows
- EU-US DPF: recipient certification verified?
- SCCs: 2021 version, TIA completed?
- UK transfers: UK IDTA or UK Addendum?

### 12. Children's Privacy (if applicable)
- Age verification mechanism?
- Parental consent for under-13 (COPPA)?
- No targeted advertising to known minors?

### 13. US State Privacy Law Compliance
- "Do Not Sell or Share" link present and functional?
- No dark patterns in opt-out flows?
- GPC signal honored where required?

### 14. NIS2 Compliance (if applicable)
- Article 21 risk management measures?
- 24-hour early warning incident reporting process?
- Supply chain security assessment?

### 15. Privacy by Design Assessment
- Default settings most privacy-protective?
- PII redacted in logs?
- Purpose-based access controls?
- Automated retention enforcement?

### 16. Breach Notification Readiness
- Incident response plan documented?
- 72-hour GDPR / 24-hour NIS2 notification capability?
- Communication templates prepared?

## Severity Rating Criteria

- **CRITICAL (Regulatory Risk)**: Clear regulatory violation with potential for enforcement action. Immediate remediation required.
- **HIGH**: Significant compliance gap or data exposure risk.
- **MEDIUM**: Partial compliance, defense-in-depth gap, or missing documentation.
- **LOW**: Best practice deviation with minimal regulatory risk.
- **INFORMATIONAL**: Privacy enhancement suggestion.

## Output

Generate a report at `docs/security/data-privacy-audit-{date}.md` where `{date}` is today's date in YYYY-MM-DD format.

Use this structure:

```markdown
# Data Privacy Audit Report
**Date**: {current date}
**Scope**: Full Application Stack
**Auditor**: Data Privacy Agent (Claude Code)
**Applicable Regulations**: GDPR, CCPA/CPRA, ePrivacy Directive, EU AI Act, NIS2

## Executive Summary
{Overall privacy posture and regulatory risk assessment}

## PII Data Map
| Data Element | Category | Collection Point | Storage Location | Access Control | Retention | Lawful Basis | Encrypted |
|---|---|---|---|---|---|---|---|

## Consent Management Assessment
## GDPR Compliance Matrix
## CCPA/CPRA Compliance Matrix
## Third-Party Data Processors
## Cookie Inventory
## AI Privacy Assessment (if applicable)
## Cross-Border Transfer Assessment

## Findings
### CRITICAL / HIGH / MEDIUM / LOW / INFORMATIONAL
#### [PRIV-001] {Finding Title}
- **Regulation**: {reference}
- **Data Category**: {PII type affected}
- **Location**: {file path or component}
- **Description**: {What was found}
- **Remediation**: {Specific fix with timeline}
- **Effort**: Trivial/Small/Medium/Large/Strategic

## Data Deletion Cascade Analysis
## Breach Notification Readiness
## Investigated but Not Reported
## Remediation Roadmap
```
