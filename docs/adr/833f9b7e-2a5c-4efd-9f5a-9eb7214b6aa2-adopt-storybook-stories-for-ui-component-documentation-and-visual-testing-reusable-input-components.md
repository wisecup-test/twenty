# Adopt Storybook Stories for UI Component Documentation and Visual Testing: Reusable Input Components

Status: proposed
Date: 2024-01-15
Deciders: Detection Pipeline (automated)

## Context

- The frontend codebase contains numerous reusable UI components for record field inputs that require consistent documentation and visual regression testing
- Component behavior and visual appearance must be validated across different states and props configurations before deployment
- Developers need an isolated environment to develop and test UI components independently from the main application context
- The pattern shows 12 Storybook story files concentrated in the record-field input components module, indicating a systematic approach to component documentation
- Visual testing and component documentation are critical for maintaining UI consistency in a CI/CD pipeline

## Problem Statement

Without a standardized approach to UI component documentation and visual testing, frontend components lack consistent validation in the CI/CD pipeline, making it difficult to catch visual regressions, document component APIs, and develop components in isolation. This leads to increased bugs in production, inconsistent component usage, and slower development cycles.

## Decision

1. MUST: All reusable UI input components MUST have corresponding Storybook story files with the `.stories.tsx` extension

## Policy Block

- MUST All reusable UI input components MUST have corresponding Storybook story files with the `.stories.tsx` extension

In scope:
- All UI components in the twenty-front package
- Reusable input field components for object records
- Components with visual presentation or user interaction
- Shared component libraries used across multiple features

Out of scope:
- Backend API services and server-side components
- Pure utility functions without UI rendering
- Internal implementation details not exposed as reusable components
- One-off components used in a single location without reuse potential

Exceptions:
- EXC-001: Component is deprecated and scheduled for removal within the current sprint
- EXC-002: Component is a temporary prototype or experimental feature not intended for production

## Rationale

- The detection of 12 story files with 91.92% confidence indicates a well-established pattern in the codebase for documenting field input components
- Storybook provides isolated component development, reducing coupling with application state and enabling faster iteration cycles
- Visual regression testing through Storybook stories catches UI bugs before they reach production, improving code quality in the CI/CD pipeline
- Component documentation through stories serves as living documentation that stays synchronized with implementation, reducing onboarding time for new developers

## Consequences

Positive:
- Improved component quality through systematic visual testing and documentation
- Faster development cycles by enabling isolated component development without running the full application
- Better collaboration between designers and developers through shared visual component library
- Reduced visual regression bugs caught early in the CI/CD pipeline
- Living documentation that automatically stays in sync with component implementation

Negative:
- Additional maintenance overhead for keeping stories updated when components change
- Increased build time in CI/CD pipeline when running visual regression tests
- Learning curve for developers unfamiliar with Storybook framework
- Potential for stories to become outdated if not properly maintained during refactoring

## Alternatives

- Use traditional unit tests with snapshot testing instead of Storybook stories (rejected)
  Rejected because: Snapshot tests don't provide visual component exploration, interactive documentation, or designer-friendly interfaces. They also produce brittle tests that break on minor changes.
  When valid: For non-visual logic-heavy components where visual presentation is not a concern
- Maintain separate component documentation in Markdown files or wiki pages (rejected)
  Rejected because: Static documentation quickly becomes outdated and doesn't provide interactive component exploration. It also creates maintenance burden of keeping docs synchronized with code.
  When valid: For high-level architectural documentation that doesn't require live component examples
- Use alternative component documentation tools like Styleguidist or Docz (rejected)
  Rejected because: Storybook has stronger ecosystem support, better CI/CD integration options, and more active community. The pattern detection shows Storybook is already established in the codebase.
  When valid: For greenfield projects where no component documentation tooling has been adopted yet

## Risks

- Stories become outdated as components evolve, leading to misleading documentation
  Mitigation: Include story file updates in definition of done for component changes. Add automated checks to detect components without stories.
  Owner: Frontend Engineering Team
- Visual regression tests may produce false positives due to environment differences or timing issues
  Mitigation: Use consistent test environments, implement retry logic, and establish clear processes for reviewing and approving visual changes
  Owner: DevOps and Frontend Teams
- Increased CI/CD pipeline execution time due to Storybook build and visual testing
  Mitigation: Optimize Storybook build configuration, use parallel test execution, and consider running visual tests only on affected components
  Owner: DevOps Team

## Implementation Notes

- Create a component story template to standardize story structure across the codebase
- Configure Storybook to automatically discover story files in `__stories__` directories
- Integrate Storybook build into the CI/CD pipeline with visual regression testing tools like Chromatic or Percy
- Document story writing guidelines in the team's frontend development guide
- Set up pre-commit hooks or CI checks to ensure new components include corresponding story files
- Consider using Storybook addons for accessibility testing, responsive design testing, and interaction testing

## Continuation Context


Verify commands:
- find packages/twenty-front/src -name '*.stories.tsx' | wc -l
- grep -r "export default.*Meta" packages/twenty-front/src/**/__stories__/*.stories.tsx
- npm run storybook:build && test -d storybook-static

Accept when:
- All reusable UI input components have corresponding story files in __stories__ directories
- Story files follow the naming convention {ComponentName}.stories.tsx and export valid Storybook Meta objects
- Storybook builds successfully without errors and generates static output
- Visual regression tests run in CI pipeline and catch component changes

## Enforcement

- Verified by: Automated CI checks that verify story files exist for all components in scope
- Verified by: Code review process that requires story files for new component PRs
- Verified by: Storybook build step in CI pipeline that fails on invalid story configurations
- Verified by: Visual regression test results reviewed before merge to main branch
- Violation handling: CI pipeline fails if new components are added without corresponding story files
- Violation handling: Pull requests are blocked until story files are added and reviewed
- Violation handling: Quarterly audits identify components missing stories for remediation
- Violation handling: Team metrics track story coverage percentage to maintain accountability
- Exception process: Developer submits exception request with justification to tech lead
- Exception process: Tech lead reviews against policy exception criteria (EXC-001, EXC-002)
- Exception process: Approved exceptions are documented in ADR exceptions log with expiration date
- Exception process: Exceptions are reviewed monthly and expired exceptions trigger remediation tasks