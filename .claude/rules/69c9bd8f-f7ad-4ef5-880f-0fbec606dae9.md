<rule_activation id="69c9bd8f-f7ad-4ef5-880f-0fbec606dae9" title="Standardize Test File Colocation with Source Code: Test Files Not" applies_to="**/*">
These rules are ALWAYS ACTIVE for all files in the codebase to enforce consistent test file organization.
</rule_activation>

### Rules

- **R-COLOC-001** MUST_NOT: Test files MUST NOT be placed in separate top-level test directories disconnected from source code
- **R-COLOC-002** MUST: Unit tests for utilities, services, and business logic MUST be colocated in `__tests__` directories adjacent to source code
- **R-COLOC-003** MUST: Component tests for React components MUST be colocated in `__tests__` directories adjacent to source code
- **R-COLOC-004** MUST: Storybook stories for UI components MUST be colocated in `__stories__` directories adjacent to source code
- **R-COLOC-005** MUST: Integration tests for closely related modules within a package MUST be colocated in `__tests__` directories
- **R-COLOC-006** MUST: Test utilities and fixtures specific to a module MUST be colocated in `__tests__` directories
- **R-COLOC-007** SHOULD: Test runners SHOULD be configured to automatically discover tests in `**/__tests__/**/*.test.{ts,tsx}` patterns
- **R-COLOC-008** SHOULD: Build tool configurations SHOULD explicitly exclude `__tests__` and `__stories__` directories from production builds

### Verify

```bash
# Count __tests__ directories
find packages -type d -name '__tests__' | wc -l

# Verify all test files are properly located in __tests__
find packages -type f -name '*.test.ts' -o -name '*.test.tsx' | grep -v '__tests__' || echo 'All test files properly located'

# Count __stories__ directories
find packages -type d -name '__stories__' | wc -l

# Verify test runner configuration uses default discovery
grep -r 'testMatch\|testRegex' --include='*.config.{js,ts}' packages/ || echo 'Using default test discovery'
```

**Accept when:**
- All test files with `.test.ts` or `.test.tsx` extensions are located within `__tests__` directories
- All Storybook story files are located within `__stories__` directories adjacent to their components
- Test runner configuration uses default discovery patterns without custom `testMatch` overrides
- No test files exist in top-level `test/` directories separate from source code
- Build tool configurations explicitly exclude `__tests__` and `__stories__` directories

<enforcement>
Claude Code MUST verify test file colocation during code generation and review. Violations MUST be flagged and corrected before accepting changes. Automated CI checks MUST fail if test files are found outside `__tests__` directories.
</enforcement>