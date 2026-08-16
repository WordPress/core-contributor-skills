# Unit tests

Use the `unit-tests` skill to audit an existing test suite for value, speed, and meaningful regression coverage.

## Audit without changing files

Invoke the skill explicitly or ask for a test-quality audit:

```text
Use $unit-tests to audit all tests in this repository.
Use $unit-tests to audit tests/unit/UserServiceTest.php.
Review the tests in src/components/__tests__ for quality and gaps.
```

The skill reports findings without editing files by default. It reads each test with its production code, runs the suite for a baseline, and returns a structured report.

## Apply improvements

Ask explicitly when you want changes:

```text
Use $unit-tests to audit tests/unit and apply the recommended improvements.
```

When authorized, the skill removes low-value tests with reasons, rewrites brittle checks, and adds tests only for genuine regression gaps. It runs the relevant suite before and after changes.

## Guardrails

- Never changes an expected value merely to make a failure pass
- Tests behavior instead of implementation details
- Treats live network or service dependencies as integration coverage
- Flags zero-test runs, skipped checks, and assertion-free tests
- Records runtime and pass, fail, and skip counts
