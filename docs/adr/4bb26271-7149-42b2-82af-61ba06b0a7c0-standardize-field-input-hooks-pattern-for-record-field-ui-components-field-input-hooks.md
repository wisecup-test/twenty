# Standardize Field Input Hooks Pattern for Record Field UI Components: Field Input Hooks

Status: proposed
Date: 2024-01-09
Deciders: Detection Pipeline (automated)

## Activation

This ADR is ACTIVE for all frontend development involving record field input components in the twenty-front package.

## Context

- The twenty-front application requires consistent handling of various field input types including files, relations (one-to-many, many-to-one, morph relations), and rich text editors
- Field input components need a uniform pattern for opening, managing state, and handling user interactions across different meta-types
- The codebase contains 7 files implementing a consistent hook-based pattern for field input management, indicating an established architectural approach
- Custom hooks provide encapsulation of field-specific logic while maintaining a consistent API surface for UI components
- The pattern emerged in the object-record module's record-field UI layer, specifically for meta-type input handling

## Problem Statement

Without a standardized pattern for field input hooks, developers may implement inconsistent approaches to managing field state, opening behaviors, and user interactions across different field types, leading to code duplication, maintenance challenges, and inconsistent user experiences.

## Decision

1. MAY: Field input hooks MAY share common utilities or base hooks for cross-cutting concerns

## Policy Block

- MAY Field input hooks MAY share common utilities or base hooks for cross-cutting concerns

In scope:
- All field input components in packages/twenty-front/src/modules/object-record/record-field/ui/meta-types/input/
- Custom hooks for managing field input state and interactions
- UI components that render field inputs for various meta-types
- New field types added to the record-field system

Out of scope:
- Field display components (non-input/read-only views)
- Field validation logic (handled separately)
- Backend field definitions and schemas
- Global state management outside the field input context

Exceptions:
- EXC-001: Simple field types (text, number, boolean) that require minimal state management and do not benefit from dedicated hooks

## Rationale

- Pattern detection identified 7 files with 88.91% confidence implementing this consistent approach, indicating strong architectural consensus
- The hook-based pattern aligns with React best practices for separating business logic from presentation components
- Dedicated hooks per field type enable type-specific optimizations while maintaining a consistent developer experience
- The modular structure (separate hooks and components directories) improves code organization and testability

## Consequences

Positive:
- Consistent API surface across all field input types reduces cognitive load for developers
- Improved code reusability and reduced duplication through standardized hook patterns
- Enhanced testability by isolating field-specific logic in dedicated hooks
- Easier onboarding for new developers due to predictable code organization
- Simplified maintenance when updating field input behavior across the application

Negative:
- Increased number of files and modules as each field type requires dedicated hook and component files
- Potential over-engineering for simple field types that may not require complex state management
- Learning curve for developers unfamiliar with the custom hook pattern
- Risk of inconsistency if developers do not follow the established naming and organizational conventions

## Alternatives

- Single monolithic hook handling all field types with conditional logic (rejected)
  Rejected because: Would create a complex, hard-to-maintain hook with excessive conditional branching and poor type safety
  When valid: Only viable for applications with 2-3 simple field types
- Inline state management within each field component without dedicated hooks (rejected)
  Rejected because: Violates separation of concerns, reduces testability, and makes logic reuse difficult
  When valid: Acceptable for prototype or proof-of-concept implementations
- Class-based component pattern with lifecycle methods (rejected)
  Rejected because: Does not align with modern React functional component best practices and hooks ecosystem
  When valid: Legacy codebases maintaining React class components

## Risks

- Developers may create field input components without following the established hook pattern, leading to architectural drift
  Mitigation: Implement linting rules and code review checklists to enforce the pattern; provide clear documentation and examples
  Owner: Frontend Architecture Team
- Hook proliferation may lead to maintenance burden as new field types are added
  Mitigation: Create shared utilities and base hooks for common functionality; regularly refactor to extract common patterns
  Owner: Engineering Team
- Inconsistent hook APIs across field types may emerge over time without governance
  Mitigation: Establish and document standard hook interface patterns; conduct periodic architectural reviews
  Owner: Frontend Architecture Team

## Implementation Notes

- When creating a new field input type, start by copying an existing hook (e.g., useOpenFilesFieldInput.tsx) as a template and adapt it to the new field type's requirements
- Place hooks in packages/twenty-front/src/modules/object-record/record-field/ui/meta-types/input/hooks/ and components in the parallel components/ directory
- Follow the naming convention: useOpen[FieldType]FieldInput for hooks and [FieldType]FieldInput for components
- Ensure hooks return consistent interface patterns (e.g., open handlers, state values, callbacks) to maintain API predictability across field types

## Continuation Context


Verify commands:
- find packages/twenty-front/src/modules/object-record/record-field/ui/meta-types/input/hooks -name 'useOpen*FieldInput.tsx' | wc -l
- grep -r 'useOpen.*FieldInput' packages/twenty-front/src/modules/object-record/record-field/ui/meta-types/input/components/ | wc -l
- test -d packages/twenty-front/src/modules/object-record/record-field/ui/meta-types/input/hooks && test -d packages/twenty-front/src/modules/object-record/record-field/ui/meta-types/input/components && echo 'Structure valid'

Accept when:
- All field input hooks follow the useOpen[FieldType]FieldInput naming pattern and are located in the hooks directory
- Each field input component in the components directory uses its corresponding dedicated hook
- The directory structure maintains separation between hooks/ and components/ within the meta-types/input module

## Enforcement

- Verified by: Automated linting rules checking for hook naming conventions and file locations
- Verified by: Code review checklist items verifying adherence to the pattern
- Verified by: CI pipeline checks validating directory structure and hook usage
- Violation handling: CI build warnings for non-compliant hook names or file locations
- Violation handling: Code review rejection for field input components not using dedicated hooks
- Violation handling: Automated refactoring suggestions provided via linting tools
- Exception process: Submit exception request to Frontend Architecture Team with justification
- Exception process: Document approved exceptions in component file comments with reference to exception ID
- Exception process: Review exceptions quarterly to determine if pattern needs evolution