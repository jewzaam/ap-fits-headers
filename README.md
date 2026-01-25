# ap-fits-headers

A tool for preserving FITS headers from file path key-value pairs into .fits and .xisf files.

## Overview

This tool extracts key-value pairs encoded in directory paths and filenames and inserts them as FITS header keywords. It only updates headers when the current value doesn't match the path value, ensuring idempotent operation.

## Usage

```powershell
python -m ap_fits_headers.preserve_headers <root_dir> --include KEY ... [--debug] [--dryrun] [--help]
```

Options:
- `root_dir`: Root directory to scan for FITS and XISF files (recursive)
- `--include KEY ...`: Specific header keys to include (required, explicit inclusion only)
- `--debug`: Enable debug output (sets logging level to DEBUG)
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

## How It Works

1. Recursively scans `root_dir` for `.fits`, `.fit`, and `.xisf` files using regex patterns from config
2. Extracts key-value pairs from directory paths and filenames using patterns like:
   - `KEY_value` (underscore separator)
   - `KEY-value` (hyphen separator)
3. Filters extracted headers to only include those specified in `--include` (explicit inclusion only)
4. For each file, compares extracted path values with existing header values
5. Updates headers only if:
   - The header doesn't exist, OR
   - The header value differs from the path value
6. Provides summary statistics at the end showing: processed, updated, no updates needed, failed

**Output Format:**
- INFO level: File counts and summary statistics
- DEBUG level (with `--debug`): Detailed processing information for each file
- WARNING level: Errors encountered during processing

## Key-Value Pair Extraction

Key-value pairs are extracted from directory names and filenames. Examples:

- Path: `/data/CAMERA_TestCam/OPTIC_C8E/file.fits`
  - Extracts: `CAMERA=TestCam`, `OPTIC=C8E`

- Path: `/data/FILTER-LRGB/TARGET-M42/image.fits`
  - Extracts: `FILTER=LRGB`, `TARGET=M42`

Keys are automatically converted to uppercase (FITS convention). Later path components take precedence if the same key appears multiple times.

## Explicit Inclusion

The tool uses explicit inclusion only. Only headers you specify in `--include` will be written to files. If you don't want a header included, simply don't add it to the `--include` list.

## Examples

### Basic Usage

```powershell
# Include CAMERA and OPTIC headers from paths
python -m ap_fits_headers.preserve_headers D:\Astrophotography\Data --include CAMERA OPTIC
```

### Dry Run

```powershell
# See what would be updated without modifying files
python -m ap_fits_headers.preserve_headers D:\Astrophotography\Data --include CAMERA OPTIC --dryrun --debug
```


### Debug Output

```powershell
# See detailed information about what's being processed
python -m ap_fits_headers.preserve_headers D:\Astrophotography\Data --include CAMERA OPTIC --debug
```

This will show:
- Each file being processed
- Key-value pairs extracted from paths
- Header comparisons and updates
- Any errors encountered

### Summary Output

After processing, the tool displays a summary:
```
INFO: Summary: 150 processed, 45 updated, 100 no updates needed, 5 failed
```

This shows:
- **processed**: Total number of files examined
- **updated**: Files that had headers modified
- **no updates needed**: Files where headers already matched path values or no matching headers found
- **failed**: Files that encountered errors during processing

## Development

### Running Tests

```powershell
make test
```

### Code Formatting

```powershell
make format
```

### Linting

```powershell
make lint
```

### Coverage

```powershell
make coverage
```

### All Checks

```powershell
make
```

## Workflow Integration

This tool is designed to be used as part of the astrophotography data processing workflow described in `../brave-new-world/PROCESS-WORKFLOW.md`. Specifically, it implements step `2b. INSERT key-value pairs into FITS headers.bat`.

The tool can be run:
- After moving files (step `2a`) to embed path metadata into file headers
- Retroactively on existing datasets to clean up path-encoded metadata
- Before master calibration frame creation (step `2d`) to ensure metadata is preserved through processing

## License

Apache-2.0
