---
description: Generate comprehensive test suite for specified files
allowed-tools: Read(*), Grep(*), Glob(*), Edit(*), Write(*), Bash(*)
---

Have the test-suite-generator agent create a comprehensive test suite for $ARGUMENTS.

Requirements:
1. Unit tests for all functions and classes
2. Integration tests for APIs if applicable
3. Edge cases and error scenarios
4. Achieve 80%+ code coverage
5. Include test execution instructions

After generating tests, run them to verify they pass.
