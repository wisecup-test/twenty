# Standardize Module Export Patterns for Component and Utility Libraries: Utility Functions Helper

Status: proposed
Date: 2024-01-09
Deciders: Detection Pipeline (automated)

## Activation

This ADR is always active for all module and library development within the codebase.

## Context

- The codebase contains multiple packages (twenty-front, twenty-website-new, twenty-zapier) that require consistent module organization and export patterns for maintainability and developer experience
- Field input components, UI meta-types, utility functions, and visual components are distributed across the codebase and need standardized public API contracts
- The pattern appears in 40 files with 87.67% confidence, indicating a well-established architectural practice for organizing library exports and module boundaries
- TypeScript/JavaScript module systems require explicit export patterns to define public APIs and prevent internal implementation details from leaking
- The facet 'api.public.contracts' suggests this pattern governs how modules expose their functionality to consumers

## Problem Statement

Without standardized module export patterns, library consumers face inconsistent import paths, unclear public API boundaries, and difficulty understanding which components and utilities are intended for external use. This leads to tight coupling, accidental dependencies on internal implementations, and increased maintenance burden when refactoring.

## Decision

1. SHOULD: Utility functions and helper modules SHOULD expose only the minimal necessary interface through their exports

## Policy Block

- SHOULD Utility functions and helper modules SHOULD expose only the minimal necessary interface through their exports

In scope:
- All TypeScript/JavaScript modules in packages/twenty-front/src/modules
- All component libraries in packages/twenty-website-new/src
- All utility modules in packages/twenty-zapier/src/utils
- Field input components under record-field/ui/meta-types/input/components
- Theme and styling modules including CSS variable definitions
- Hook modules and custom React hooks for component behavior

Out of scope:
- Third-party npm packages and external dependencies
- Build configuration files (yarn.config.cjs, webpack configs)
- Test files and test utilities (unless they are part of a testing library)
- Internal build artifacts and generated code
- Documentation files and markdown content

Exceptions:
- EXC-001: Direct imports from implementation files are permitted in test files (*.test.ts, *.spec.ts, __stories__/*) for testing internal behavior
- EXC-002: Migration periods when refactoring existing modules to new export patterns

## Rationale

- The pattern appears in 40 files across multiple packages with 87.67% confidence, demonstrating a mature and widely-adopted architectural practice
- Standardized export patterns create clear API boundaries that enable safe refactoring of internal implementations without breaking consumers
- Index files serve as documentation of the public API surface, making it easier for developers to discover available functionality
- Consistent module organization reduces cognitive load and improves developer productivity when working across different packages

## Consequences

Positive:
- Clear separation between public APIs and internal implementation details enables safer refactoring
- Improved discoverability of available components and utilities through well-defined module exports
- Reduced coupling between modules as consumers depend only on stable public interfaces
- Better IDE autocomplete and TypeScript type inference through explicit export declarations
- Easier onboarding for new developers who can understand module boundaries through index files

Negative:
- Additional maintenance overhead of keeping index files synchronized with module contents
- Potential for circular dependencies if index files are not carefully structured
- Slightly longer import paths in some cases when using barrel exports
- Risk of over-exporting internal utilities if public API boundaries are not carefully considered

## Alternatives

- Allow direct imports from any file without index file exports (rejected)
  Rejected because: This approach provides no API boundaries, makes refactoring dangerous, and creates tight coupling between modules. The evidence shows the codebase has moved away from this pattern.
  When valid: Only appropriate for very small projects with a single developer
- Use a monolithic single index file at the package root exporting everything (rejected)
  Rejected because: This creates a flat namespace that doesn't scale well and makes it difficult to understand module organization. The evidence shows hierarchical index files are preferred.
  When valid: May work for very small libraries with fewer than 10 exports
- Use explicit package.json exports field to define public API (deferred)
  Rejected because: While this provides stronger enforcement, it requires build tooling changes and may not be compatible with current module resolution strategy
  When valid: Should be reconsidered when migrating to ESM modules or publishing packages to npm

## Risks

- Developers may bypass index files and import directly from implementation files, undermining the API boundary
  Mitigation: Implement ESLint rules to detect and prevent direct imports from non-index files. Add CI checks to enforce the pattern.
  Owner: Engineering team / DevOps
- Index files may become out of sync with actual module contents, leading to missing or incorrect exports
  Mitigation: Add automated tests that verify all public components are exported. Use TypeScript strict mode to catch missing exports at compile time.
  Owner: Engineering team
- Circular dependencies may emerge when index files import from each other
  Mitigation: Use dependency analysis tools (e.g., madge) in CI to detect circular dependencies. Structure modules to avoid cross-dependencies.
  Owner: Architecture team

## Implementation Notes

- Start by creating index.ts/tsx files in each module directory that export the public API surface
- For component libraries, group exports by component type (inputs, displays, layouts) in separate index files
- Use named exports rather than default exports to improve refactoring safety and IDE support
- Document the public API contract in comments within index files, noting any deprecations or usage guidelines
- Consider using TypeScript's 'export type' for type-only exports to optimize bundle size

## Continuation Context


Verify commands:
- grep -r "from.*\/components\/[^/]*\/[^'\"]*\.tsx" --include="*.ts" --include="*.tsx" --exclude-dir=node_modules --exclude-dir=__stories__ | grep -v "index\.tsx" || echo 'No direct component imports found'
- find packages/*/src -name 'index.ts' -o -name 'index.tsx' | wc -l
- npx madge --circular --extensions ts,tsx packages/twenty-front/src packages/twenty-website-new/src

Accept when:
- All module directories containing public components or utilities have index.ts/tsx files
- No direct imports from implementation files are found outside of test files (grep verification passes)
- Circular dependency analysis shows zero circular dependencies between modules
- TypeScript compilation succeeds without errors related to missing or incorrect exports

## Enforcement

- Verified by: ESLint rules checking import patterns (e.g., no-restricted-imports)
- Verified by: CI pipeline running grep-based verification commands
- Verified by: Code review checklist requiring index file updates for new modules
- Verified by: TypeScript compiler strict mode catching missing exports
- Violation handling: CI build fails if direct imports from non-index files are detected
- Violation handling: ESLint errors block PR merge until resolved
- Violation handling: Code review feedback requires correction before approval
- Violation handling: Automated comments on PRs highlighting violations with remediation guidance
- Exception process: Document exception request in PR description with justification
- Exception process: Obtain tech lead approval for temporary exceptions during migration
- Exception process: Add TODO comment with tracking ticket for resolving the exception
- Exception process: Set a deadline for removing the exception (typically within 2 sprints)