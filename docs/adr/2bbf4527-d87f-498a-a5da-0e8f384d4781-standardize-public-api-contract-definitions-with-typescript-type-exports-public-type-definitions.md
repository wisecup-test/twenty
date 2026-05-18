# Standardize Public API Contract Definitions with TypeScript Type Exports: Public Type Definitions

Status: proposed
Date: 2024-01-15
Deciders: Detection Pipeline (automated)

## Context

- The codebase contains multiple public-facing API integrations including Zapier integration packages and website components that expose data structures to external consumers
- External API consumers require stable, well-documented type contracts to integrate reliably with the platform without breaking changes
- The pattern appears across 69 files with 89.17% confidence, indicating a systematic approach to defining public API contracts through TypeScript type definitions
- Integration packages (twenty-zapier) and public website components (twenty-website-new) serve as the primary touchpoints for external API consumption
- Type safety and contract clarity are critical for third-party integrations to prevent runtime errors and maintain API stability across versions

## Problem Statement

External API consumers need predictable, type-safe interfaces to integrate with the platform, but without standardized contract definitions, API changes can introduce breaking changes, runtime errors, and integration failures. The lack of explicit public API contract standards makes it difficult to maintain backward compatibility and communicate API expectations to external developers.

## Decision

1. MUST: Public API type definitions MUST be exported from dedicated type files (*.types.ts) to enable external consumption

## Policy Block

- MUST Public API type definitions MUST be exported from dedicated type files (*.types.ts) to enable external consumption

In scope:
- All Zapier integration trigger and action definitions
- Public webhook payload structures
- REST API request/response types exposed to external consumers
- GraphQL schema types that map to public API surfaces
- SDK and client library type exports
- Public website component props that represent API data structures

Out of scope:
- Internal service-to-service communication types
- Database entity definitions not exposed via public APIs
- Private utility types used only within implementation code
- Test fixtures and mock data types
- Internal UI component props that don't represent API contracts

Exceptions:
- EXC-001: Rapid prototyping of experimental API features in alpha/beta stages
- EXC-002: Emergency security patches requiring immediate breaking changes

## Rationale

- The pattern detected across 69 files with 89.17% confidence indicates a mature, established practice of using TypeScript type definitions for public API contracts
- Explicit type exports in integration packages (twenty-zapier) demonstrate the need for external consumers to have compile-time type safety when integrating with the platform
- Separating type definitions into dedicated files (*.types.ts) enables clean contract boundaries and makes it easier to track API surface changes during code reviews
- The presence of this pattern in both integration packages and website components suggests a platform-wide commitment to type-safe external interfaces

## Consequences

Positive:
- External developers gain compile-time type safety when integrating with the platform, reducing integration errors
- API contract changes become explicit and reviewable through type definition changes, improving change management
- Documentation can be auto-generated from TypeScript types, reducing documentation drift
- Breaking changes are caught earlier in development through TypeScript compiler errors in dependent projects

Negative:
- Requires additional maintenance overhead to keep type definitions synchronized with implementation
- May slow down rapid prototyping if strict type contracts are enforced too early in feature development
- Increases initial development time for new API endpoints due to explicit type definition requirements
- Can create friction when refactoring internal implementations that are constrained by public type contracts

## Alternatives

- Use runtime validation schemas (e.g., Zod, Yup) as the source of truth for API contracts instead of TypeScript types (rejected)
  Rejected because: Runtime schemas don't provide compile-time safety for TypeScript consumers and add runtime overhead. TypeScript types can be derived from schemas if needed, but types-first approach is more idiomatic for TypeScript projects.
  When valid: Consider for APIs that need runtime validation guarantees or are consumed by non-TypeScript clients
- Generate TypeScript types from OpenAPI/Swagger specifications (deferred)
  Rejected because: Not rejected, but deferred pending evaluation of OpenAPI adoption. Could complement current approach by generating types from specs.
  When valid: Valid if the platform adopts OpenAPI as the primary API specification format
- Use GraphQL schema as single source of truth with generated TypeScript types (rejected)
  Rejected because: Not all public APIs are GraphQL-based (e.g., Zapier triggers, webhooks). GraphQL type generation should complement, not replace, explicit TypeScript type definitions for non-GraphQL APIs.
  When valid: Valid specifically for GraphQL API endpoints where schema-first development is preferred

## Risks

- Type definitions may drift from actual runtime behavior if not validated, creating false sense of type safety
  Mitigation: Implement integration tests that validate runtime payloads against TypeScript type definitions using type guards or runtime validation libraries
  Owner: API Platform Team
- Overly strict type contracts may prevent necessary API evolution and force breaking changes
  Mitigation: Use TypeScript utility types (Partial, Pick, Omit) to create flexible contract variations and adopt additive-only changes where possible
  Owner: API Design Working Group
- External consumers may depend on undocumented type properties, making it difficult to remove deprecated fields
  Mitigation: Implement API usage analytics to track field usage and provide long deprecation windows with clear migration paths
  Owner: Developer Relations Team

## Implementation Notes

- Create a shared types package (e.g., @twenty/api-types) that can be consumed by both internal services and external integration packages
- Establish naming conventions for type files: use *.types.ts for public API contracts and *.internal.ts for implementation-only types
- Add ESLint rules to prevent accidental export of internal types from public API packages
- Document the public API type export pattern in the developer onboarding guide with examples from the Zapier integration
- Set up automated type compatibility checks in CI to detect breaking changes before they reach production

## Continuation Context


Verify commands:
- grep -r "export.*type.*" packages/twenty-zapier/src --include="*.types.ts" | wc -l
- find packages/*/src -name "*.types.ts" -type f | xargs grep -l "export" | wc -l
- npx tsc --noEmit --project packages/twenty-zapier/tsconfig.json

Accept when:
- All integration packages contain at least one *.types.ts file with exported type definitions
- TypeScript compilation succeeds without errors for all public API packages
- Grep commands identify exported types in dedicated type definition files across integration packages

## Enforcement

- Verified by: Automated CI checks that validate TypeScript compilation for all public API packages
- Verified by: Code review checklist requiring explicit type definitions for new API endpoints
- Verified by: API documentation generation pipeline that fails if public endpoints lack type definitions
- Violation handling: CI pipeline fails if public API packages have TypeScript compilation errors
- Violation handling: Pull requests adding new API endpoints without type definitions are blocked until types are added
- Violation handling: Quarterly API contract audits identify and remediate missing or incomplete type definitions
- Exception process: Developer submits exception request to API Lead with justification and remediation timeline
- Exception process: API Lead reviews request and approves/rejects within 2 business days
- Exception process: Approved exceptions are documented in ADR updates and tracked in technical debt backlog
- Exception process: All exceptions must include a concrete plan to achieve compliance within 1 sprint cycle