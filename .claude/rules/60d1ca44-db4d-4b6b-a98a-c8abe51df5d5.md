<rule_activation id="60d1ca44-db4d-4b6b-a98a-c8abe51df5d5" title="Adopt Jest as Standard Testing Framework for TypeScript Components: Test Suites Achieve" applies_to="**/*.test.ts,**/*.test.tsx">
These rules are ALWAYS ACTIVE for all TypeScript test files in the monorepo. Jest is the standard testing framework for all TypeScript packages (twenty-zapier, twenty-front, etc.).
</rule_activation>

### Rules

- **R-JEST-001** SHOULD: Test suites SHOULD achieve minimum 80% code coverage for utility functions, authentication, and business logic.
- **R-JEST-002** MUST: All test files MUST follow the naming convention `__tests__/*.test.ts` or `__tests__/*.test.tsx`.
- **R-JEST-003** MUST: All TypeScript packages in the monorepo MUST use Jest as their testing framework.
- **R-JEST-004** MUST: Each package with TypeScript code MUST include Jest as a dependency and have a valid `jest.config.js` or `jest.config.ts` file.
- **R-JEST-005** SHOULD: Test utilities and shared mocks SHOULD be extracted to common packages (e.g., `@twenty/test-utils`) to reduce duplication.
- **R-JEST-006** MUST: Unit tests for utility functions, helpers, and pure functions MUST be included in scope.
- **R-JEST-007** MUST: Integration tests for API endpoints, triggers, and CRUD operations MUST be included in scope.
- **R-JEST-008** MUST: Component tests for UI elements and input validation logic MUST be included in scope.
- **R-JEST-009** MUST: Authentication and authorization flow testing MUST be included in scope.

### Verify

```bash
# Verify all test files use Jest framework
find packages -type f -name '*.test.ts' -o -name '*.test.tsx' | xargs grep -L 'from.*jest' | wc -l | grep -q '^0$'

# Verify Jest is configured as a dependency in all packages
grep -r "\"jest\":" packages/*/package.json | wc -l | awk '{if($1>0) exit 0; else exit 1}'

# Verify minimum number of test files following convention
npm run test -- --listTests | grep -E '__tests__/.*\.test\.ts$' | wc -l | awk '{if($1>=11) exit 0; else exit 1}'
```

**Accept when:**
- All TypeScript test files use Jest framework and follow `__tests__/*.test.ts` naming convention
- Each package with TypeScript code includes Jest as a dependency and has a valid jest.config file
- CI pipeline successfully executes Jest test suites for all packages and reports coverage metrics
- Test suites achieve minimum 80% code coverage for utility functions, authentication, and business logic

<enforcement>
Claude Code MUST NOT skip or defer verification. All pull requests with non-Jest test files or incorrect naming patterns MUST be blocked from merging. CI pipeline MUST fail if Jest configuration is missing from packages containing TypeScript code.
</enforcement>