# Adopt Jest as Standard Testing Framework for TypeScript Components: Test Suites Achieve

Status: proposed
Date: 2024-01-15
Deciders: Detection Pipeline (automated)

## Context

- The codebase contains multiple TypeScript packages (twenty-zapier, twenty-front) requiring consistent testing infrastructure across frontend and integration components
- Test files follow a standardized naming convention (__tests__/*.test.ts) indicating an established testing pattern across 11 files with 91.85% confidence
- The project requires testing capabilities for utility functions, authentication flows, triggers, CRUD operations, and UI component validation
- A unified testing framework reduces cognitive overhead for developers working across multiple packages and ensures consistent test execution in CI/CD pipelines

## Problem Statement

Without a standardized testing framework, teams may adopt inconsistent testing approaches across packages, leading to fragmented test execution, incompatible tooling configurations, and increased maintenance burden in CI/CD pipelines.

## Decision

1. SHOULD: Test suites SHOULD achieve minimum 80% code coverage for utility functions, authentication, and business logic

## Policy Block

- SHOULD Test suites SHOULD achieve minimum 80% code coverage for utility functions, authentication, and business logic

In scope:
- All TypeScript packages in the monorepo (twenty-zapier, twenty-front, etc.)
- Unit tests for utility functions, helpers, and pure functions
- Integration tests for API endpoints, triggers, and CRUD operations
- Component tests for UI elements and input validation logic
- Authentication and authorization flow testing

Out of scope:
- End-to-end tests that may use Playwright, Cypress, or other E2E frameworks
- Performance and load testing which may require specialized tools
- Manual testing and exploratory testing activities
- Third-party library code that comes with its own test suite

Exceptions:
- EXC-001: Legacy packages migrating from another testing framework may temporarily maintain dual test infrastructure during transition period

## Rationale

- Jest provides comprehensive TypeScript support with minimal configuration, reducing setup complexity across multiple packages in the monorepo
- The detected pattern shows 11 files across critical paths (authentication, CRUD operations, triggers, UI validation) already following this convention with 91.85% confidence
- Jest's built-in mocking, assertion library, and coverage reporting eliminate the need for multiple testing dependencies, simplifying CI/CD pipeline configuration
- Standardizing on Jest enables shared test utilities, consistent developer experience, and easier onboarding for new team members

## Consequences

Positive:
- Unified testing approach reduces context switching for developers working across multiple packages
- Consistent test execution in CI/CD pipelines with predictable performance characteristics
- Shared Jest configuration and test utilities can be extracted to common packages, reducing duplication
- Strong TypeScript integration provides type-safe test authoring and better IDE support

Negative:
- Teams already invested in alternative frameworks (Mocha, Jasmine) face migration costs
- Jest's default configuration may require customization for specific use cases (e.g., DOM testing, module mocking)
- Larger test suite execution times compared to lighter-weight test runners for simple unit tests
- Learning curve for developers unfamiliar with Jest's API and conventions

## Alternatives

- Use Vitest as the primary testing framework for faster execution and native ESM support (rejected)
  Rejected because: Existing codebase shows established Jest patterns across 11 files; migration would require significant refactoring without clear performance benefits for current test suite size
  When valid: Consider for new greenfield projects or when test execution time becomes a bottleneck (>10 minutes)
- Allow each package to choose its own testing framework based on team preference (rejected)
  Rejected because: Fragmented tooling increases CI/CD complexity, prevents sharing of test utilities, and creates inconsistent developer experience across the monorepo
  When valid: Only valid for packages with unique requirements that cannot be met by Jest (e.g., specialized hardware testing)
- Use native Node.js test runner (node:test) to eliminate external dependencies (rejected)
  Rejected because: Native test runner lacks maturity, ecosystem tooling, and advanced features like snapshot testing and comprehensive mocking that Jest provides
  When valid: Reconsider when Node.js native test runner reaches feature parity and has strong TypeScript support

## Risks

- Jest version upgrades may introduce breaking changes requiring coordinated updates across all packages
  Mitigation: Pin Jest major versions in root package.json, test upgrades in isolated branch, maintain upgrade documentation
  Owner: Platform Engineering Team
- Test execution time may grow linearly with codebase size, slowing CI/CD pipelines
  Mitigation: Implement Jest's --maxWorkers configuration, use test sharding in CI, monitor test execution metrics
  Owner: DevOps Team
- Developers may write tests that pass locally but fail in CI due to environment differences
  Mitigation: Standardize Jest configuration across environments, use Docker for consistent test execution, document environment setup
  Owner: Engineering Team

## Implementation Notes

- Configure Jest in the monorepo root with shared configuration that can be extended by individual packages using jest.config.js or jest.config.ts
- Establish naming conventions: test files in __tests__ directories with .test.ts extension, test utilities in __tests__/helpers or __tests__/fixtures
- Set up pre-commit hooks to run Jest tests on changed files, and configure CI to run full test suite on pull requests
- Create shared test utilities package (@twenty/test-utils) for common mocks, fixtures, and helper functions used across packages

## Continuation Context


Verify commands:
- find packages -type f -name '*.test.ts' -o -name '*.test.tsx' | xargs grep -L 'from.*jest' | wc -l | grep -q '^0$'
- grep -r "\"jest\":" packages/*/package.json | wc -l | awk '{if($1>0) exit 0; else exit 1}'
- npm run test -- --listTests | grep -E '__tests__/.*\.test\.ts$' | wc -l | awk '{if($1>=11) exit 0; else exit 1}'

Accept when:
- All TypeScript test files use Jest framework and follow __tests__/*.test.ts naming convention
- Each package with TypeScript code includes Jest as a dependency and has a valid jest.config file
- CI pipeline successfully executes Jest test suites for all packages and reports coverage metrics

## Enforcement

- Verified by: Automated CI/CD pipeline checks that run Jest tests on every pull request
- Verified by: Code review process verifying new test files follow Jest conventions and naming patterns
- Verified by: Static analysis tools scanning for test file patterns and Jest configuration presence
- Violation handling: Pull requests with non-Jest test files or incorrect naming patterns are blocked from merging
- Violation handling: CI pipeline fails if Jest configuration is missing from packages containing TypeScript code
- Violation handling: Automated comments on PRs guide developers to correct test file structure and framework usage
- Exception process: Developer submits exception request to engineering lead with justification for alternative approach
- Exception process: Exception request must include: specific technical limitation, proposed alternative, impact assessment, and timeline
- Exception process: Approved exceptions are documented in ADR amendments and tracked in project documentation