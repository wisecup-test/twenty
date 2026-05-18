# Adopt Zapier Integration Pattern for External API Exposure: External Integrations Not

Status: proposed
Date: 2024-01-15
Deciders: Detection Pipeline (automated)

## Context

- The system requires integration with third-party automation platforms to enable users to connect their data with external services
- Zapier is a widely-adopted integration platform that provides standardized patterns for triggers, actions, and data synchronization
- The codebase contains 47 files implementing Zapier-specific integration patterns including triggers for record listing, object discovery, and real-time record updates
- External API consumers need a consistent, well-documented interface for accessing system data and functionality without direct database access
- The pattern demonstrates high consistency (89.95% confidence) across website components and integration packages, indicating a deliberate architectural choice

## Problem Statement

How should the system expose its functionality to external third-party platforms and automation tools in a way that is secure, maintainable, and follows industry-standard integration patterns while enabling users to build workflows without custom code?

## Decision

1. MUST_NOT: External integrations MUST NOT expose internal database schemas or implementation details directly

## Policy Block

- MUST_NOT External integrations MUST NOT expose internal database schemas or implementation details directly

In scope:
- All Zapier integration triggers and actions
- External API packages (twenty-zapier, similar third-party integrations)
- Public API documentation and marketing materials
- Type definitions for external data contracts
- Object metadata and discovery endpoints

Out of scope:
- Internal GraphQL APIs used by first-party clients
- Direct database access patterns
- Internal service-to-service communication
- Admin-only APIs and tooling
- Development and testing utilities

Exceptions:
- EXC-001: Legacy integrations built before this pattern was established may use alternative structures

## Rationale

- Zapier is an industry-standard integration platform with over 5,000 app integrations, providing immediate value to users who want to connect their data
- The pattern evidence shows 47 files with 89.95% confidence, indicating this is a mature, well-established architectural decision
- Separating external integrations into dedicated packages (twenty-zapier) maintains clear architectural boundaries and enables independent versioning
- Standardized trigger patterns (list_record_ids, find_object_names, trigger_record) align with Zapier's best practices and reduce integration maintenance burden

## Consequences

Positive:
- Users can build powerful automation workflows without writing code or understanding internal system architecture
- Clear package boundaries make it easier to maintain, test, and version external integrations independently
- Following Zapier's standard patterns reduces onboarding friction for users familiar with the platform
- Type-safe data contracts reduce integration bugs and improve developer experience
- Marketing and documentation co-location improves discoverability and adoption of integration features

Negative:
- Additional maintenance burden for keeping integration packages synchronized with core API changes
- Zapier-specific patterns may not translate well to other integration platforms, potentially requiring duplicate implementations
- External integration packages add complexity to the build and deployment pipeline
- Type definitions must be maintained in multiple locations (core API and integration packages)

## Alternatives

- Build a generic webhook-based integration API without platform-specific packages (rejected)
  Rejected because: Generic webhooks require users to write custom code and lack the discoverability and ease-of-use that platform-specific integrations provide
  When valid: For advanced users who need custom integration logic not supported by standard platforms
- Embed integration logic directly in the core application without separate packages (rejected)
  Rejected because: Mixing external integration concerns with core business logic violates separation of concerns and makes the codebase harder to maintain
  When valid: Never - clear architectural boundaries are essential for maintainability
- Support multiple integration platforms (Zapier, Make, n8n) with a unified abstraction layer (deferred)
  Rejected because: Not rejected - this is a potential future enhancement that builds on the current pattern
  When valid: When user demand for additional platforms justifies the development investment

## Risks

- Zapier API changes or deprecations could break existing integrations
  Mitigation: Implement comprehensive integration tests, monitor Zapier changelog, and version integration packages independently
  Owner: Integration team
- Type definition drift between core API and integration packages could cause runtime errors
  Mitigation: Use automated type generation from core API schemas, implement contract testing, and enforce strict TypeScript compilation
  Owner: Engineering team
- External integrations may expose sensitive data or enable unauthorized access if not properly secured
  Mitigation: Implement OAuth 2.0 authentication, rate limiting, audit logging, and scope-based permissions for all external API endpoints
  Owner: Security team

## Implementation Notes

- Create new integration packages under packages/twenty-{platform} following the established twenty-zapier structure
- Define all external data contracts in dedicated .types.ts files with comprehensive JSDoc documentation
- Implement standard trigger patterns: list_record_ids for polling, trigger_record for webhooks, and find_object_names for dynamic configuration
- Use Vite or similar modern bundlers to ensure compatibility with platform requirements and optimize bundle size
- Co-locate integration documentation and visual components in website sections for improved discoverability
- Implement comprehensive error handling and logging to aid debugging of integration issues

## Continuation Context


Verify commands:
- find packages -type d -name 'twenty-*' | grep -E '(zapier|integration)' | wc -l
- grep -r 'list_record_ids\|find_object_names\|trigger_record' packages/twenty-zapier/src/triggers/ | wc -l
- find packages/twenty-zapier -name '*.types.ts' -o -name 'data.types.ts' | wc -l
- grep -r 'vite.config' packages/twenty-zapier/ | wc -l

Accept when:
- At least one integration package exists under packages/ with platform-specific naming (e.g., twenty-zapier)
- Standard trigger patterns (list_record_ids, find_object_names, trigger_record) are implemented in the integration package
- Type definition files exist for external data contracts
- Modern build tooling (Vite or equivalent) is configured for integration packages

## Enforcement

- Verified by: Automated CI checks verify integration package structure and naming conventions
- Verified by: Code review checklist includes verification of type definitions and standard trigger patterns
- Verified by: Integration tests validate Zapier trigger implementations against platform requirements
- Verified by: Architecture review for new external integration packages
- Violation handling: CI pipeline fails if integration packages are missing required trigger patterns or type definitions
- Violation handling: Pull requests are blocked if external API changes are not reflected in integration package types
- Violation handling: Security scanning flags any direct database access or internal schema exposure in integration code
- Violation handling: Monthly architecture reviews identify and remediate pattern violations
- Exception process: Submit exception request to architecture review board with detailed justification
- Exception process: Document alternative approach and rationale in integration package README
- Exception process: Obtain approval from both security and engineering leads for any deviations from standard patterns
- Exception process: Add exception to tracking system with review date for potential future remediation