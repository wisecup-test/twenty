# Standardize Test Utilities in Dedicated __tests__ Directories: Test Files Utility

Status: proposed
Date: 2024-01-09
Deciders: Detection Pipeline (automated)

## Activation

This ADR is ACTIVE for all test file organization and utility function placement within the codebase.

## Context

- The codebase contains utility functions for input validation and sanitization that require comprehensive testing coverage
- Test files for utility functions are organized in dedicated __tests__ directories adjacent to the source code, following Jest conventions
- The pattern appears in the object-record module's input handling utilities, specifically for sanitization validation and multi-item field computation
- This organizational pattern supports test discoverability, maintains clear separation between production and test code, and aligns with modern JavaScript/TypeScript testing practices
- The testing.mocking facet indicates these tests likely involve mock data and isolated unit testing of utility functions

## Problem Statement

Without a consistent approach to organizing test files and utilities, test code becomes scattered across the codebase, making it difficult to locate tests, maintain test coverage, and ensure consistent testing practices. The lack of standardization can lead to duplicate test utilities, inconsistent naming conventions, and reduced developer productivity when writing or updating tests.

## Decision

1. MUST: Test files for utility functions MUST be placed in a __tests__ directory adjacent to the source code being tested

## Policy Block

- MUST Test files for utility functions MUST be placed in a __tests__ directory adjacent to the source code being tested

In scope:
- All utility functions in the packages/twenty-front/src/modules directory structure
- Input validation and sanitization utilities
- Field computation and transformation utilities
- Any reusable utility functions that require unit testing

Out of scope:
- End-to-end tests which should be placed in a separate e2e directory
- Integration tests that span multiple packages
- Performance or load testing utilities
- Test fixtures and mock data files which may have their own organization

Exceptions:
- EXC-001: Legacy test files that predate this standard and are scheduled for migration
- EXC-002: Third-party library integration tests that follow the library's recommended structure

## Rationale

- The detected pattern shows consistent use of __tests__ directories in 2 files with 92.50% confidence, indicating an established practice worth standardizing
- Placing tests adjacent to source code improves discoverability and makes it easier to maintain test coverage as code evolves
- The __tests__ directory convention is widely adopted in the JavaScript/TypeScript ecosystem and supported by default in Jest and other testing frameworks
- This pattern supports the testing.mocking facet by providing a clear location for isolated unit tests with mocked dependencies

## Consequences

Positive:
- Improved test discoverability as developers can immediately locate tests for any utility function
- Consistent test organization across the entire codebase reduces cognitive load
- Better IDE support and tooling integration with standardized test file locations
- Easier to maintain test coverage metrics and identify untested code
- Simplified CI/CD pipeline configuration with predictable test file locations

Negative:
- Requires migration effort for existing test files that don't follow this pattern
- May create many small __tests__ directories throughout the codebase, increasing directory count
- Developers must remember to create __tests__ directories for new utilities
- Potential for inconsistency during transition period while legacy tests are migrated

## Alternatives

- Centralized test directory structure mirroring source code hierarchy (rejected)
  Rejected because: Creates distance between source and test files, making it harder to maintain tests alongside code changes. Requires duplicating directory structure in test folder.
  When valid: May be appropriate for integration tests or end-to-end tests that span multiple modules
- Co-locate test files directly alongside source files without __tests__ directory (rejected)
  Rejected because: Clutters source directories with test files and makes it harder to exclude tests from production builds. Less clear separation of concerns.
  When valid: Could work for very small modules with only one or two files
- Use .spec.ts extension instead of .test.ts (rejected)
  Rejected because: The detected pattern uses .test.ts extension consistently. Changing would require codebase-wide migration with no clear benefit.
  When valid: Both conventions are valid; consistency matters more than the specific choice

## Risks

- Incomplete migration of existing tests could lead to inconsistent patterns across the codebase
  Mitigation: Create migration plan with automated tooling to move test files. Add linting rules to enforce new structure for new tests.
  Owner: Engineering team
- Developers may forget to create __tests__ directories for new utility functions
  Mitigation: Add pre-commit hooks and CI checks to verify test coverage. Include test file creation in code review checklist.
  Owner: Engineering team
- Build tools or bundlers might accidentally include test files in production builds
  Mitigation: Configure build tools to explicitly exclude __tests__ directories and .test.ts files. Verify with bundle analysis.
  Owner: DevOps team

## Implementation Notes

- Configure Jest or your test runner to automatically discover test files in __tests__ directories with the pattern **/__tests__/**/*.test.ts
- Update the project's testing documentation and contribution guidelines to reflect this standard
- Create a code snippet or template in the IDE to quickly scaffold new test files in the correct location
- Consider adding ESLint rules or custom linting to enforce test file naming and location conventions
- For existing codebases, create a migration script to move test files to __tests__ directories and update import paths

## Continuation Context


Verify commands:
- find packages/twenty-front/src -name '*.test.ts' -o -name '*.test.tsx' | grep -v '__tests__' && echo 'FAIL: Test files found outside __tests__ directories' || echo 'PASS'
- grep -r "describe\|test\|it" packages/twenty-front/src --include='*.ts' --include='*.tsx' --exclude-dir=__tests__ --exclude='*.test.ts' --exclude='*.test.tsx' | grep -v node_modules && echo 'FAIL: Test code found in non-test files' || echo 'PASS'
- jest --listTests | grep -E 'packages/twenty-front/src/.*/__tests__/.*\.test\.(ts|tsx)$' && echo 'PASS: Test files follow naming convention' || echo 'FAIL'

Accept when:
- All test files for utility functions are located in __tests__ directories adjacent to source code
- Test file names match source file names with .test.ts or .test.tsx extension
- No test files exist outside of __tests__ directories in the modules directory structure
- Jest test discovery successfully finds all test files using the standard pattern

## Enforcement

- Verified by: Automated CI pipeline checks for test file locations and naming conventions
- Verified by: Pre-commit hooks that validate test file organization
- Verified by: Code review checklist includes verification of test file placement
- Verified by: Periodic automated audits of test file structure
- Violation handling: CI pipeline fails if test files are found outside __tests__ directories
- Violation handling: Code review requires correction before merge approval
- Violation handling: Automated PR comments flag violations with guidance on correct structure
- Violation handling: Monthly reports identify non-compliant test files for remediation
- Exception process: Developer submits exception request with technical justification to tech lead
- Exception process: Tech lead reviews and approves/rejects within 2 business days
- Exception process: Approved exceptions are documented in code comments with ADR reference
- Exception process: Exceptions are reviewed quarterly to determine if they can be eliminated