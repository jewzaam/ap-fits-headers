# Test Plan

> This document describes the testing strategy for this project. It serves as the single source of truth for testing decisions and rationale.

## Overview

**Project:** ap-preserve-header
**Primary functionality:** Extracts key-value pairs from directory paths and filenames and inserts them as FITS header keywords into .fits and .xisf files.

## Testing Philosophy

This project follows the [ap-base Testing Standards](https://github.com/jewzaam/ap-base/blob/main/standards/testing.md).

Key testing principles for this project:

- Tests verify actual header values written to FITS/XISF files, not just that code runs without errors
- All file I/O uses programmatically generated minimal test files (no Git LFS)
- Mocking is used for CLI/main entry point tests; real file operations for header manipulation tests

## Test Categories

### Unit Tests

Tests for isolated functions with mocked dependencies.

| Module | Function | Test Coverage | Notes |
|--------|----------|---------------|-------|
| `preserve_headers.py` | `extract_key_value_pairs()` | Path parsing, multiple keys, separators, case normalization | Delegates to ap-common's `get_file_headers` |
| `preserve_headers.py` | `should_include_header()` | Include list filtering, case insensitivity | Pure logic |
| `preserve_headers.py` | `get_header_value()` | FITS header reads, XISF dict reads, missing keys, numeric values | Uses real astropy/xisf objects |
| `preserve_headers.py` | `set_header_value()` | FITS header writes, XISF dict writes | Uses real astropy/xisf objects |
| `preserve_headers.py` | `main()` | CLI argument parsing, flag combinations, quiet/debug modes | Mocks `preserve_headers` and `setup_logging` |

### Integration Tests

Tests for multiple components working together.

| Workflow | Components | Test Coverage | Notes |
|----------|------------|---------------|-------|
| Full header preservation | `extract_key_value_pairs` + `preserve_headers` + `update_fits_header` | End-to-end FITS file processing | Uses real FITS files in tmp dirs |
| XISF header preservation | `extract_key_value_pairs` + `preserve_headers` + `update_xisf_headers` | End-to-end XISF file processing | Uses real XISF files in tmp dirs |
| Dry run mode | `preserve_headers` with `dryrun=True` | Verifies no file modifications | Confirms headers unchanged |
| Include list filtering | `preserve_headers` with selective `include_headers` | Only specified keys written | Verifies excluded keys absent |
| Idempotent operation | `preserve_headers` with pre-existing matching headers | Skips files already matching | Verifies no unnecessary writes |

## Untested Areas

| Area | Reason Not Tested |
|------|-------------------|
| `config.py` constants | Configuration constants only, no logic |
| Third-party library behavior (astropy, xisf) | Tested through public interface, not directly |
| Logging output | Logging is not the primary functionality |

## Bug Fix Testing Protocol

All bug fixes to existing functionality **must** follow TDD:

1. Write a failing test that exposes the bug
2. Verify the test fails before implementing the fix
3. Implement the fix
4. Verify the test passes
5. Verify reverting the fix causes the test to fail again
6. Commit test and fix together with issue reference

### Regression Tests

| Issue | Test | Description |
|-------|------|-------------|
| — | — | No regression tests yet |

## Coverage Goals

**Target:** 80%+ line coverage

**Philosophy:** Coverage measures completeness, not quality. A test that executes code without meaningful assertions provides no value. Focus on:

- Testing behavior, not implementation details
- Covering edge cases and error conditions
- Ensuring assertions verify expected outcomes

## Running Tests

```bash
# Run all tests
make test

# Run with coverage
make coverage

# Run specific test
pytest tests/test_preserve_headers.py::TestExtractKeyValuePairs::test_extract_simple_key_value
```

## Test Data

Test data is:
- Generated programmatically in fixtures where possible
- Stored in `tests/fixtures/` when static files are needed
- Documented in `tests/fixtures/README.md`

**No Git LFS** - all test data must be small (< 100KB) or generated.

## Maintenance

When modifying this project:

1. **Adding features**: Add tests for new functionality after implementation
2. **Fixing bugs**: Follow TDD protocol above (test first, then fix)
3. **Refactoring**: Existing tests should pass without modification (behavior unchanged)
4. **Removing features**: Remove associated tests

## Changelog

| Date | Change | Rationale |
|------|--------|-----------|
| 2026-02-15 | Initial test plan | Project creation |
