<rule_activation id="99bdfa3b-dba9-48a0-be1b-7a22af7b89d6" title="Standardize Test File Colocation with Source Code: Integration Tests That" applies_to="**/*">
These rules are ALWAYS ACTIVE for all files in the codebase to ensure consistent test file organization and colocation with source code.
</rule_activation>

### Rules

- **R-COLOC-001** MUST: Unit tests for utilities, services, and business logic MUST be placed in `__tests__` directories adjacent to the source code they test.
- **R-COLOC-002** MUST: Component tests for React components MUST be placed in `__tests__` directories adjacent to the component source files.
- **R-COLOC-003** MUST: Storybook stories for UI components MUST be placed in `__stories__` directories adjacent to the component source files.
- **R-COLOC-004** MUST: Test files MUST follow naming conventions of `*.test.ts` or `*.test.tsx`.
- **R-COLOC-005** MAY: Integration tests that span multiple modules MAY be placed in a dedicated integration test directory at the package level.
- **R-COLOC-006** MUST NOT: End-to-end tests, performance tests, security tests, cross-package integration tests, and infrastructure tests MUST NOT follow this colocation pattern.
- **R-COLOC-007** SHOULD: Test utilities and fixtures specific to a module SHOULD be colocated within the module's `__tests__` directory.
- **R-COLOC-008** MUST: Build tool configurations MUST explicitly exclude `__tests__` and `__stories__` directories from production bundles.
- **R-COLOC-009** MUST: Test runner configuration (Jest/Vitest) MUST use automatic discovery patterns for `**/__tests__/**/*.test.{ts,tsx}` without custom testMatch overrides.

### Verify

```bash
# Count __tests__ directories
find packages -type d -name '__tests__' | wc -l

# Verify all test files are properly located in __tests__
find packages -type f -name '*.test.ts' -o -name '*.test.tsx' | grep -v '__tests__' || echo 'All test files properly located'

# Count __stories__ directories
find packages -type d -name '__stories__' | wc -l

# Verify test runner uses default discovery
grep -r 'testMatch\|testRegex' --include='*.config.{js,ts}' packages/ || echo 'Using default test discovery'
```

**Accept when:**
- All test files with `.test.ts` or `.test.tsx` extensions are located within `__tests__` directories adjacent to source code
- All Storybook story files are located within `__stories__` directories adjacent to their components
- Test runner configuration uses default discovery patterns without custom testMatch overrides
- No test files exist in top-level `test/` directories separate from source code
- Build tool configurations explicitly exclude `__tests__` and `__stories__` directories from production builds
- Integration tests spanning multiple modules are documented and placed at package level when appropriate

<enforcement>
Claude Code MUST verify test file colocation during code review and MUST NOT approve pull requests that violate R-COLOC-001 through R-COLOC-009. Automated CI checks MUST fail if test files are found outside `__tests__` directories. ESLint rules MUST enforce test file location and naming conventions.
</enforcement>