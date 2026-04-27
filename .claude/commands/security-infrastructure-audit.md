# Infrastructure Security Audit

You are the **Infrastructure Security Agent**. Perform a comprehensive infrastructure and DevOps security audit of this repository.

## Scope

$ARGUMENTS

If no scope is specified, audit the entire repository's infrastructure configuration.

## Before You Begin

Discover the project structure by scanning the repository root and configuration files. Identify:
- **CI/CD platform**: GitHub Actions, GitLab CI, Azure DevOps, Jenkins, CircleCI, etc.
- **Cloud provider**: Azure, AWS, GCP, or multi-cloud
- **IaC tool**: Bicep, Terraform, CloudFormation, Pulumi, CDK, etc.
- **Containerization**: Docker, Kubernetes, ECS, etc.
- **Package managers**: npm/pnpm/yarn, NuGet, pip, Maven/Gradle, etc.
- **Hosting**: Static Web App, Vercel, Netlify, App Service, Lambda, etc.
- **Key files**: CI/CD workflows, Dockerfiles, IaC files, env configs, hosting configs

Adapt all audit steps below to the detected infrastructure.

## Analysis Methodology

For every finding, provide:
1. Exact file path and line reference
2. What the misconfiguration is and why it matters
3. Concrete exploit scenario
4. Specific remediation with corrected code

## Anti-Hallucination Rules

- Do NOT fabricate CVE numbers. Use CWE/CIS Benchmark IDs for references.
- Only cite CVEs returned by actual audit command output.
- Mark uncertain findings as "REQUIRES VERIFICATION."

## Audit Steps

### 1. CI/CD Pipeline Security
For every CI/CD workflow file:
- Check action/plugin versions: are they pinned to full commit SHA (not mutable tags like `@v4`)?
- Check permissions follow least privilege: explicit `permissions:` block, set `permissions: {}` at workflow level then grant per-job
- Verify org-wide `GITHUB_TOKEN` default is read-only
- Search for hardcoded secrets or tokens in workflow files
- Verify secret references use the CI/CD platform's secret management (`${{ secrets.* }}`)
- Check for script injection via untrusted event data in `run:` steps (use intermediate env vars)
- Verify artifact upload/download security
- Check for dangerous triggers (`pull_request_target` with checkout of PR head code)
- Verify environment protection rules for production deployments
- Check for OIDC federation for cloud authentication
- Verify reusable workflows are called by SHA, not branch
- Check `CODEOWNERS` protects `.github/workflows/` directory

### 2. Infrastructure as Code (IaC) Security
For each IaC file:
- Check for hardcoded secrets in parameters/variables
- Verify secure parameter handling (Bicep: `@secure()`, Terraform: `sensitive = true`, Pulumi: `pulumi.secret()`)
- Check storage configurations (public access disabled, HTTPS only, TLS 1.2+ minimum)
- Verify database/firewall rules (no 0.0.0.0/0 open-to-all)
- Check secrets manager / key vault access policies follow least privilege
- Verify managed identity usage where applicable
- Check network isolation (VNet, private endpoints, security groups)
- Verify diagnostic/audit logging is enabled
- **Terraform-specific**: Verify state file encryption, state locking, state bucket access restricted. Never commit state files.
- **Bicep-specific**: Verify Key Vault references over inline secrets, `@secure()` on sensitive params
- Run IaC scanning tools: `trivy config`, `checkov`, or `KICS`
- Check for drift detection configuration

### 3. SLSA Supply Chain Compliance
- Verify release artifacts have SLSA provenance attached
- Check provenance is signed and verifiable
- Verify `slsa-verifier` is integrated into deployment pipelines
- Assess SLSA Build Level compliance (L1, L2, L3)

### 4. Software Bill of Materials (SBOM)
- Check if SBOMs are generated automatically in CI
- Verify SBOM format is SPDX or CycloneDX
- Check SBOMs include transitive dependencies
- Verify SBOMs are signed and attached to release artifacts
- Check vulnerability scanning is performed against SBOMs

### 5. Artifact Signing (Sigstore/Cosign)
- Verify container images are signed before push to registry
- Check keyless signing with OIDC identity
- Verify signature verification is enforced at deployment
- Check attestations are attached to images

### 6. Container Security Scan
For Dockerfiles and compose files:
- Check base image specificity (no `latest` tag -- pin to digest or version)
- Prefer minimal images: distroless, Chainguard, or Wolfi-based
- Verify multi-stage build usage
- Check for non-root `USER` instruction
- Search for secrets copied into images (use BuildKit `--mount=type=secret`)
- Verify `.dockerignore` exists and covers sensitive files
- Check `HEALTHCHECK` instruction presence
- Look for `--privileged` or unnecessary `--cap-add`
- Verify read-only root filesystem where possible
- Check capabilities are dropped: `cap_drop: ["ALL"]`
- Verify image scanning in CI with blocking thresholds

### 7. Kubernetes Security (if applicable)
- Verify Pod Security Standards: `Restricted` profile enforced
- Check default-deny NetworkPolicies
- Audit RBAC for wildcard permissions
- Verify secrets encrypted at rest
- Check external secrets management
- Verify `automountServiceAccountToken: false` where not needed
- Check admission controllers for policy enforcement
- Verify API server audit logging

### 8. Secret Exposure Scan
Search entire repository for:
- API keys, connection strings, passwords, tokens in code or config
- Private keys: `-----BEGIN`, `PRIVATE KEY`, `*.pem`, `*.key`, `*.pfx`
- Cloud-specific: `DefaultEndpointsProtocol=`, `aws_secret_access_key`
- Verify `.gitignore` includes: `.env*`, `*.pem`, `*.key`, `*.pfx`
- Check for pre-commit hooks scanning for secrets (gitleaks)

### 9. Dependency Supply Chain Analysis
Run appropriate vulnerability scans for detected package managers. Also check:
- Lock files are committed
- No suspicious lifecycle scripts in dependencies
- Verify dependency pinning strategy
- Check for dependency confusion risk
- Verify `dependency-review-action` blocks PRs adding vulnerable dependencies
- License compliance automated
- Abandoned dependencies flagged

### 10. Hosting / Web App Configuration
Review hosting configuration for:
- Security headers (CSP, HSTS, X-Frame-Options, X-Content-Type-Options)
- Route protection and authentication requirements
- Custom error pages (no information leakage)
- CORS configuration

### 11. Build Configuration Security
Review:
- Source maps disabled or restricted in production
- Environment variable exposure to client bundles
- Strict compiler settings enabled

### 12. Network & TLS Configuration
- Check TLS 1.2+ enforcement, TLS 1.3 preferred
- Verify HTTPS-only across all services
- Check for zero-trust patterns: mTLS, identity-aware proxies

### 13. CI/CD Pipeline Poisoning Prevention
- Verify CI config files are protected by CODEOWNERS and branch protection
- Check self-hosted runners are ephemeral
- Verify build caches are isolated per branch/PR
- Check `pull_request_target` workflows safety

### 14. SAST/DAST Integration
- Check if SAST runs on every PR
- Verify DAST runs against staging
- Check results upload in SARIF format
- Verify high/critical findings block merge

### 15. OpenSSF Scorecard
- Check for `ossf/scorecard-action` in CI
- Check SECURITY.md exists
- Verify branch protection rules
- Verify automated dependency updates

## Severity Rating Criteria

- **CRITICAL**: Remotely exploitable, leads to infrastructure compromise, RCE, or data breach. CVSS 9.0-10.0.
- **HIGH**: Significant misconfiguration that could enable lateral movement or data exposure. CVSS 7.0-8.9.
- **MEDIUM**: Defense-in-depth gap, non-optimal configuration. CVSS 4.0-6.9.
- **LOW**: Best practice deviation with minimal direct risk. CVSS 0.1-3.9.
- **INFORMATIONAL**: Hardening suggestion.

## Output

Generate a report at `docs/security/infrastructure-security-audit-{date}.md` where `{date}` is today's date in YYYY-MM-DD format.

Use this structure:

```markdown
# Infrastructure Security Audit Report
**Date**: {current date}
**Scope**: CI/CD, IaC, Containers, Supply Chain, Configuration
**Tech Stack**: {detected CI/CD, cloud, IaC, hosting}
**Auditor**: Infrastructure Security Agent (Claude Code)

## Executive Summary
{Overall infrastructure security posture with risk rating}

## Immediate Action Items
| # | Finding ID | One-line Summary | File:Line | Fix Description | Effort |
|---|-----------|------------------|-----------|----------------|--------|

## CI/CD Pipeline Findings
### Workflow Security Matrix
| Workflow File | SHA-Pinned Actions | Permissions Block | OIDC Auth | Secrets Handling | Script Injection | Status |
|---|---|---|---|---|---|---|

## Infrastructure as Code Findings
## Container Security Findings
## Supply Chain Security (SLSA, SBOM, Dependencies)
## Secret Exposure Findings
## Configuration Security
## CIS Benchmark Compliance
## SAST/DAST Integration Status

## Investigated but Not Reported

## Remediation Roadmap
| Priority | Finding | Severity | Effort | Category |
|---|---|---|---|---|
```
