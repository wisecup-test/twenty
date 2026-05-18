<rule_activation id="7ad8cfac-bafa-493d-904a-2c3ad8e2da21" title="Standardize Public API Contract Definitions with TypeScript Type Exports: Public Type Definitions" applies_to="**/*">
These rules are ALWAYS ACTIVE for all files matching public API type definitions, integration packages, and external-facing API contracts.
</rule_activation>

### Rules

- **R-API-001** SHOULD: Public API type definitions SHOULD include JSDoc comments documenting field purposes, constraints, and examples.

### Scope

**In scope:**
- All Zapier integration trigger and action definitions
- Public webhook payload structures
- REST API request/response types exposed to external consumers
- GraphQL schema types that map to public API surfaces
- SDK and client library type exports
- Public website component props that represent API data structures

**Out of scope:**
- Internal service-to-service communication types
- Database entity definitions not exposed via public APIs
- Private utility types used only within implementation code
- Test fixtures and mock data types
- Internal UI component props that don't represent API contracts

**Exceptions:**
- EXC-001: Rapid prototyping of experimental API features in alpha/beta stages
- EXC-002: Emergency security patches requiring immediate breaking changes

### Verify

```bash
# Count exported types in Zapier integration type files
grep -r "export.*type.*" packages/twenty-zapier/src --include="*.types.ts" | wc -l

# Find all type definition files with exports across packages
find packages/*/src -name "*.types.ts" -type f | xargs grep -l "export" | wc -l

# Verify TypeScript compilation succeeds for public API packages
npx tsc --noEmit --project packages/twenty-zapier/tsconfig.json
```

**Accept when:**
- All integration packages contain at least one `*.types.ts` file with exported type definitions
- TypeScript compilation succeeds without errors for all public API packages
- Grep commands identify exported types in dedicated type definition files across integration packages

### Enforcement

- **Verified by:** Automated CI checks that validate TypeScript compilation for all public API packages
- **Verified by:** Code review checklist requiring explicit type definitions for new API endpoints
- **Verified by:** API documentation generation pipeline that fails if public endpoints lack type definitions
- **Violation handling:** CI pipeline fails if public API packages have TypeScript compilation errors
- **Violation handling:** Pull requests adding new API endpoints without type definitions are blocked until types are added
- **Violation handling:** Quarterly API contract audits identify and remediate missing or incomplete type definitions
- **Exception process:** Developer submits exception request to API Lead with justification and remediation timeline
- **Exception process:** API Lead reviews request and approves/rejects within 2 business days
- **Exception process:** Approved exceptions are documented in ADR updates and tracked in technical debt backlog
- **Exception process:** All exceptions must include a concrete plan to achieve compliance within 1 sprint cycle

<enforcement>
Claude Code MUST NOT skip or defer verification. All public API type definitions MUST be validated against TypeScript compilation and type export requirements before acceptance.
</enforcement>