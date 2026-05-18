# Standardize Test File Colocation with Source Code: Test Files Not

Status: proposed
Date: 2024-01-09
Deciders: Detection Pipeline (automated)

## Context

- The codebase contains 23 files following a consistent pattern of colocating test files with source code using __tests__ and __stories__ directories
- Test files are organized adjacent to the code they test rather than in separate top-level test directories, improving discoverability and maintainability
- The pattern is particularly prevalent in the twenty-front and twenty-zapier packages, indicating a deliberate architectural choice for component and utility testing
- Storybook stories are colocated with UI components using __stories__ directories, enabling rapid visual testing and documentation
- This pattern supports CI/CD workflows by making test discovery automatic and ensuring tests remain synchronized with implementation changes

## Problem Statement

Development teams need a consistent, scalable approach to organizing test files that minimizes the cognitive overhead of locating tests, ensures tests remain synchronized with implementation code, and supports automated test discovery in CI/CD pipelines without requiring complex configuration or manual test registration.

## Decision

1. MUST_NOT: Test files MUST NOT be placed in separate top-level test directories disconnected from source code

## Policy Block

- MUST_NOT Test files MUST NOT be placed in separate top-level test directories disconnected from source code

In scope:
- Unit tests for utilities, services, and business logic
- Component tests for React components
- Storybook stories for UI components
- Integration tests for closely related modules within a package
- Test utilities and fixtures specific to a module

Out of scope:
- End-to-end tests that test the entire application
- Performance and load tests
- Security penetration tests
- Cross-package integration tests
- Infrastructure and deployment tests

Exceptions:
- EXC-001: Legacy code being migrated may temporarily maintain separate test directories during transition
- EXC-002: Third-party library wrappers where test structure must match upstream conventions

## Rationale

- The pattern appears in 23 files with 91.89% confidence, indicating strong adoption across the codebase and demonstrating proven effectiveness
- Colocation reduces the cognitive distance between implementation and tests, making it easier for developers to maintain test coverage as code evolves
- Modern test runners (Jest, Vitest) automatically discover tests in __tests__ directories without requiring manual configuration, streamlining CI/CD pipeline setup
- This pattern aligns with industry best practices from React, Node.js, and TypeScript communities, reducing onboarding friction for new developers

## Consequences

Positive:
- Improved test discoverability - developers can immediately locate tests for any module without searching the codebase
- Reduced test maintenance burden - when code is moved or refactored, tests move with it automatically
- Simplified CI/CD configuration - test runners automatically discover all tests without glob patterns or manual registration
- Better code review experience - test changes appear alongside implementation changes in pull requests
- Enhanced developer experience - IDE navigation between source and tests becomes trivial

Negative:
- Directory structure becomes deeper with additional __tests__ and __stories__ subdirectories
- May create confusion for developers accustomed to centralized test directories
- Requires consistent discipline across all packages to maintain the pattern
- Can lead to duplication of test utilities if not properly shared across modules

## Alternatives

- Centralized test directory mirroring source structure (e.g., src/ and test/ at root) (rejected)
  Rejected because: Creates cognitive overhead in locating tests, requires manual synchronization when refactoring, and increases risk of orphaned tests when code is moved or deleted
  When valid: May be appropriate for legacy codebases with established patterns or when organizational policy mandates separation
- Inline test files alongside source (e.g., Button.tsx and Button.test.tsx in same directory) (rejected)
  Rejected because: Clutters source directories with test files, makes it harder to exclude tests from production builds, and mixes concerns in file listings
  When valid: Acceptable for very small modules or when using build tools with sophisticated filtering
- Package-level test directory with flat structure (rejected)
  Rejected because: Does not scale well as codebase grows, requires unique test file names across entire package, and loses contextual relationship between tests and source
  When valid: May work for very small packages with fewer than 20 test files

## Risks

- Inconsistent adoption across packages leads to fragmented testing patterns
  Mitigation: Implement linting rules to enforce directory structure, provide migration guides, and conduct code review training
  Owner: Engineering team leads
- Test utilities and shared fixtures may be duplicated across __tests__ directories
  Mitigation: Create shared test utility packages (e.g., @twenty/test-utils) and document their usage in testing guidelines
  Owner: Platform team
- Build tools may accidentally include test files in production bundles
  Mitigation: Configure bundlers to explicitly exclude __tests__ and __stories__ directories, verify with bundle analysis in CI
  Owner: DevOps team

## Implementation Notes

- Configure test runners (Jest/Vitest) to automatically discover tests in **/__tests__/**/*.test.{ts,tsx} patterns
- Update .gitignore and build tool configurations to exclude __tests__ and __stories__ directories from production builds
- Create ESLint rules to enforce test file naming conventions and directory structure
- Provide code scaffolding templates that automatically create __tests__ directories when generating new modules
- Document the pattern in the project's contributing guidelines with examples from existing codebase

## Continuation Context


Verify commands:
- find packages -type d -name '__tests__' | wc -l
- find packages -type f -name '*.test.ts' -o -name '*.test.tsx' | grep -v '__tests__' || echo 'All test files properly located'
- find packages -type d -name '__stories__' | wc -l
- grep -r 'testMatch\|testRegex' --include='*.config.{js,ts}' packages/ || echo 'Using default test discovery'

Accept when:
- All test files with .test.ts or .test.tsx extensions are located within __tests__ directories
- All Storybook story files are located within __stories__ directories adjacent to their components
- Test runner configuration uses default discovery patterns without custom testMatch overrides
- No test files exist in top-level test/ directories separate from source code

## Enforcement

- Verified by: Automated CI checks scanning for misplaced test files
- Verified by: ESLint rules enforcing test file location and naming conventions
- Verified by: Code review checklist items for new test files
- Verified by: Pre-commit hooks validating test file structure
- Violation handling: CI pipeline fails if test files are found outside __tests__ directories
- Violation handling: ESLint errors block pull request merges
- Violation handling: Automated comments on pull requests guide developers to correct structure
- Violation handling: Quarterly audits identify and remediate violations in legacy code
- Exception process: Developer submits exception request to tech lead with justification
- Exception process: Tech lead reviews against exception criteria (EXC-001, EXC-002)
- Exception process: Approved exceptions documented in package README or ADR amendment
- Exception process: Exceptions reviewed quarterly for potential removal