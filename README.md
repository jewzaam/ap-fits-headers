# ap-fits-headers

A simple tool for preserving FITS headers from file path key-value pairs into .fits and .xisf files.

## Overview

This tool extracts key-value pairs encoded in directory paths and filenames and inserts them as FITS header keywords. It only updates headers when the current value doesn't match the path value, ensuring idempotent operation.

## Usage

```powershell
python -m ap_fits_headers.preserve_headers <root_dir> --include KEY ... [--debug] [--dryrun] [--help]
```

Options:
- `root_dir`: Root directory to scan for FITS and XISF files (recursive)
- `--include KEY ...`: Specific header keys to include (required, explicit inclusion only)
- `--debug`: Enable debug output
- `--dryrun`: Perform dry run without actually modifying files
- `--help`: Show help message and exit

## Installation

### From Source (Development)

```powershell
make install-dev
```

This installs the package in editable mode along with all dependencies (including `ap-common` from git) and development tools.

### From Git Repository (One-liner)

```powershell
pip install git+https://github.com/jewzaam/ap-fits-headers.git
```

This installs the package directly from the GitHub repository without requiring a local checkout.

### Uninstallation

```powershell
make uninstall
```

## Examples

Given a file path like:
```
D:\Astrophotography\Data\CAMERA_ASI294MC\OPTIC_C8E\TARGET_M42\image.fits
```

Running:
```powershell
python -m ap_fits_headers.preserve_headers D:\Astrophotography\Data --include CAMERA OPTIC
```

Will extract `CAMERA=ASI294MC` and `OPTIC=C8E` from the path and write them as FITS header keywords in the file. The `TARGET` key-value pair is ignored since it's not in the `--include` list.

**Note**: Multiple keys are specified as space-separated values after `--include`. The `nargs="+"` argument parser accepts one or more values.

## License

Apache-2.0
