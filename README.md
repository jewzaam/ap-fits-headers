# ap-preserve-header

[![Test](https://github.com/jewzaam/ap-preserve-header/workflows/Test/badge.svg)](https://github.com/jewzaam/ap-preserve-header/actions/workflows/test.yml) [![Coverage](https://github.com/jewzaam/ap-preserve-header/workflows/Coverage%20Check/badge.svg)](https://github.com/jewzaam/ap-preserve-header/actions/workflows/coverage.yml) [![Lint](https://github.com/jewzaam/ap-preserve-header/workflows/Lint/badge.svg)](https://github.com/jewzaam/ap-preserve-header/actions/workflows/lint.yml) [![Format](https://github.com/jewzaam/ap-preserve-header/workflows/Format%20Check/badge.svg)](https://github.com/jewzaam/ap-preserve-header/actions/workflows/format.yml) [![Type Check](https://github.com/jewzaam/ap-preserve-header/workflows/Type%20Check/badge.svg)](https://github.com/jewzaam/ap-preserve-header/actions/workflows/typecheck.yml)
[![Python 3.10+](https://img.shields.io/badge/python-3.10+-blue.svg)](https://www.python.org/downloads/) [![Code style: black](https://img.shields.io/badge/code%20style-black-000000.svg)](https://github.com/psf/black)

A simple tool for preserving FITS headers from file path key-value pairs into .fits and .xisf files.

## Overview

This tool extracts key-value pairs encoded in directory paths and filenames and inserts them as FITS header keywords. It only updates headers when the current value doesn't match the path value, ensuring idempotent operation.

## Documentation

This tool is part of the astrophotography pipeline. For comprehensive documentation including workflow guides and integration with other tools, see:

- **[Pipeline Overview](https://github.com/jewzaam/ap-base/blob/main/docs/index.md)** - Full pipeline documentation
- **[Workflow Guide](https://github.com/jewzaam/ap-base/blob/main/docs/workflow.md)** - Detailed workflow with diagrams
- **[ap-preserve-header Reference](https://github.com/jewzaam/ap-base/blob/main/docs/tools/ap-preserve-header.md)** - API reference for this tool

## Usage

```powershell
python -m ap_preserve_header <root_dir> --include KEY ... [--debug] [--dryrun] [--quiet] [--help]
```

Options:
- `root_dir`: Root directory to scan for FITS and XISF files (recursive)
- `--include KEY ...`: Specific header keys to include (required, explicit inclusion only)
- `--debug`: Enable debug output
- `--dryrun`: Perform dry run without actually modifying files
- `--quiet`, `-q`: Suppress progress output
- `--help`: Show help message and exit

## Installation

### Development

```powershell
make install-dev
```

This installs the package in editable mode along with all dependencies (including `ap-common` from git) and development tools.

### From Git

```powershell
pip install git+https://github.com/jewzaam/ap-preserve-header.git
```

This installs the package directly from the GitHub repository without requiring a local checkout.

## Examples

Given a file path like:
```
D:\Astrophotography\Data\CAMERA_ASI294MC\OPTIC_C8E\TARGET_M42\image.fits
```

Running:
```powershell
python -m ap_preserve_header D:\Astrophotography\Data --include CAMERA OPTIC
```

Will extract `CAMERA=ASI294MC` and `OPTIC=C8E` from the path and write them as FITS header keywords in the file. The `TARGET` key-value pair is ignored since it's not in the `--include` list.

**Note**: Multiple keys are specified as space-separated values after `--include`. The `nargs="+"` argument parser accepts one or more values.

### Preserving Multiple Keys

```powershell
# Preserve camera, filter, and exposure metadata
python -m ap_preserve_header D:\Astrophotography\10_Blink --include CAMERA FILTER EXPOSURE
```

For a file at:
```
D:\Astrophotography\10_Blink\CAMERA_ASI2600MM\FILTER_Ha\EXPOSURE_300\light_001.fits
```

This will write:
- `CAMERA = 'ASI2600MM'`
- `FILTER = 'Ha'`
- `EXPOSURE = 300`

### Dry Run Mode

```powershell
# Preview changes without modifying files
python -m ap_preserve_header D:\Astrophotography --include CAMERA OPTIC TARGET --dryrun
```

Shows which files would be updated and what header changes would be made, without actually writing to files.
