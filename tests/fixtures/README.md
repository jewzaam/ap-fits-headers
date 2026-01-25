# Test Fixtures

This directory contains real FITS and XISF files from actual data gathering for use in tests.

## Required Files

- `sample.fits` - A real FITS file from your data gathering
- `sample.xisf` - A real XISF file from your data gathering

## Adding Files

1. **Copy your real files here:**
   ```bash
   cp /path/to/your/real/file.fits tests/fixtures/sample.fits
   cp /path/to/your/real/file.xisf tests/fixtures/sample.xisf
   ```

2. **Track with git LFS:**
   ```bash
   git lfs track "tests/fixtures/*.fits"
   git lfs track "tests/fixtures/*.xisf"
   git add .gitattributes tests/fixtures/sample.fits tests/fixtures/sample.xisf
   git commit -m "Add real test fixture files"
   ```

   Note: The `.gitattributes` file is already configured to track these files with LFS.

## Immutability

These files are **immutable** during testing. The test fixtures in `conftest.py` automatically copy these files to temporary directories before any test runs. This ensures:

- The original files remain untouched
- Tests can modify copies without affecting the source
- Multiple tests can run in parallel without conflicts

## File Requirements

- **FITS files**: Should be valid FITS files that can be read by `astropy.io.fits`
- **XISF files**: Should be valid XISF files that can be read by the `xisf` library

The files don't need to have any specific headers or content - they just need to be valid files that the libraries can read and write.
