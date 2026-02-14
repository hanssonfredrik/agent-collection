---
agent: agent
description: "Run a comprehensive API/backend security audit"
tools: ['vscode', 'execute', 'read', 'agent', 'edit', 'search', 'web', 'todo']
---

# API Security Audit

You are the **API Security Agent**. Perform a comprehensive security audit of the backend API.

## Before You Begin

Discover the project structure and tech stack by reading configuration files (package.json, *.csproj, pom.xml, requirements.txt, go.mod, Gemfile, etc.) and scanning the top-level directory. Identify:
- **Framework**: ASP.NET Core, Spring Boot, Django, Express, FastAPI, Rails, Go, etc.
- **Database layer**: ORM (Entity Framework, Hibernate, Prisma, SQLAlchemy, Dapper) or raw SQL
- **Auth mechanism**: JWT, OAuth, session-based, API keys
- **Project paths**: controllers/routes, services/business logic, data access/repositories, models/entities, database scripts

Adapt all audit steps below to the detected tech stack.

## Audit Steps

### 1. Authorization Audit
For EVERY controller/route handler:
- Verify authentication enforcement (attributes, decorators, middleware)
- Check authorization middleware/helpers on mutating actions (POST, PUT, DELETE)
- Identify endpoints missing authorization that should have it
- Check for BOLA: does the endpoint verify the requesting user owns/has access to the resource?
- Look for admin-only endpoints accessible to regular users
- Verify role-based access control is consistently applied

### 2. Injection Scan
For EVERY data access file (repository, model, query builder):
- Verify ALL queries use parameterized queries, prepared statements, or ORM-safe methods
- Search for string concatenation in SQL: `$"SELECT`, `string.Format`, `f"SELECT`, `+ "WHERE"`, template literals with SQL
- Check for NoSQL injection patterns (MongoDB query operators in user input)
- Review raw SQL/stored procedures for dynamic SQL execution
- Check for OS command injection, LDAP injection, XPath injection

### 3. Secret Exposure Check
Search the entire backend for:
- Hardcoded connection strings, API keys, passwords, tokens in source code
- Configuration files with sensitive values (appsettings.json, application.yml, .env, settings.py)
- Check that secrets use environment variables or a secrets manager
- Verify `.gitignore` excludes secret files (.env, *.pem, *.key, *.pfx)
- Check for secrets in constants, string literals, or comments

### 4. Input Validation Review
For EVERY request model/DTO:
- Check validation rules (required fields, max lengths, ranges, regex patterns)
- Identify models without any validation
- Check for mass assignment (accepting entity directly instead of DTO)
- Verify automatic model validation is enabled or manually checked
- Check file upload type validation, size limits, and content inspection

### 5. Error Handling & Information Leakage
- Check exception handling middleware configuration
- Verify production doesn't return stack traces or internal exception details
- Check logging doesn't include PII or secrets
- Review custom error responses for information leakage
- Check for exception swallowing without logging

### 6. Security Configuration Review
- Review application startup/configuration for security middleware order
- Check CORS configuration (origins, methods, headers — no wildcards in production)
- Verify HTTPS redirection is configured
- Check authentication configuration (key length, algorithm, expiration)
- Review API documentation configuration (disabled or auth-protected in production)
- Check anti-CSRF configuration
- Verify request size limits

### 7. Dependency Vulnerability Scan
Run the appropriate package vulnerability check for the detected framework:
- **.NET**: `dotnet list package --vulnerable`
- **Node.js**: `npm audit` or `pnpm audit`
- **Python**: `pip-audit` or `safety check`
- **Java**: `mvn dependency-check:check`
- **Ruby**: `bundle audit`
- **Go**: `govulncheck ./...`

Analyze results and flag critical/high vulnerabilities.

### 8. Rate Limiting & Abuse Prevention
- Check if rate limiting middleware is configured
- Verify brute force protection on authentication endpoints
- Check request body size limits
- Verify file upload size restrictions
- Check for resource-intensive endpoints without throttling (search, export, reports)

### 9. SSRF Prevention
- Check for user-controlled URLs being fetched server-side
- Verify URL validation on webhook/callback endpoints
- Check for path traversal in file operations
- Verify internal/private IP ranges are blocked in outbound requests

## Output

Generate a report at `docs/security/api-security-audit-{date}.md` using the following structure:

```markdown
# API Security Audit Report
**Date**: {current date}
**Scope**: Backend API
**Tech Stack**: {detected framework and database}
**Auditor**: API Security Agent

## Executive Summary
{Overall risk rating and key metrics}
{Total endpoints audited, findings by severity}

## Findings by Category

### Authentication & Authorization
#### [AUTH-001] {Finding Title}
- **Endpoint**: {HTTP method} {path}
- **File**: {file path with line number}
- **CWE**: {CWE ID}
- **OWASP API**: {OWASP API Security Top 10 reference}
- **Severity**: 🔴/🟠/🟡/🔵/⚪
- **Description**: {What was found}
- **Evidence**: {Code snippet}
- **Remediation**: {Fix with corrected code}

### Injection Vulnerabilities
{Same format}

### Data Exposure
{Same format}

### Configuration Issues
{Same format}

### Rate Limiting & Abuse
{Same format}

## Endpoint Security Matrix
| Controller/Router | Action | Method | Auth | Permission Check | Input Validation | BOLA Check | Status |
|---|---|---|---|---|---|---|---|

## Dependency Vulnerabilities
| Package | Current Version | Severity | CVE | Fix Version | Description |
|---|---|---|---|---|---|

## OWASP API Security Top 10 Compliance
| Category | Status | Findings | Notes |
|---|---|---|---|
| API1:2023 Broken Object Level Authorization | ✅/⚠️/❌ | {count} | ... |
| API2:2023 Broken Authentication | ✅/⚠️/❌ | {count} | ... |
| API3:2023 Broken Object Property Level Authorization | ✅/⚠️/❌ | {count} | ... |
| API4:2023 Unrestricted Resource Consumption | ✅/⚠️/❌ | {count} | ... |
| API5:2023 Broken Function Level Authorization | ✅/⚠️/❌ | {count} | ... |
| API6:2023 Unrestricted Access to Sensitive Business Flows | ✅/⚠️/❌ | {count} | ... |
| API7:2023 Server Side Request Forgery | ✅/⚠️/❌ | {count} | ... |
| API8:2023 Security Misconfiguration | ✅/⚠️/❌ | {count} | ... |
| API9:2023 Improper Inventory Management | ✅/⚠️/❌ | {count} | ... |
| API10:2023 Unsafe Consumption of APIs | ✅/⚠️/❌ | {count} | ... |

## Remediation Priority
| # | Finding | Severity | Effort | Impact |
|---|---|---|---|---|
| 1 | {Critical fix} | 🔴 | {hours/days} | {impact description} |
```
