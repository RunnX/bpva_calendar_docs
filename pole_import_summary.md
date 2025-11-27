# Pole Import Feature - Implementation Summary

## Created Files

### 1. **lib/utils/pole_import_util.dart**

Core utility class for importing poles from CSV files.

**Key Features:**

- Parses CSV files with the BPVA Essx Inventory format
- Handles multi-column layout (3 column groups at positions 2, 6, 10)
- Auto-detects brand from row 2 (e.g., "ESSX Inventory")
- Generates unique barcodes: `POLE-{brand}-{serialNumber}` or `POLE-{brand}-{lengthFeetInches}-{weight}`
- Returns detailed ImportResult with success/error counts
- Validates length formats (e.g., "12' 7"", "12'")
- Skips empty rows and invalid data gracefully

### 2. **lib/scripts/import_poles.dart**

Standalone command-line script for importing poles.

**Usage:**

```bash
# Update adminUserId in the script first
flutter run lib/scripts/import_poles.dart
```

**Features:**

- Initializes Firebase
- Imports from default CSV path
- Displays detailed console output
- Shows added poles and errors

### 3. **POLE_IMPORT_README.md**

Comprehensive documentation for the import feature.

**Includes:**

- CSV format specifications
- Two import methods (in-app and CLI)
- Error handling guide
- Troubleshooting tips
- Security notes
- Example output

## Modified Files

### 1. **pubspec.yaml**

- Added `csv: ^6.0.0` dependency
- Added `assets/import-data/` to assets list

### 2. **lib/screens/admin/pole_inventory_screen.dart**

Added in-app import functionality:

- Import button (📤) in AppBar (admin/coach only)
- `_showImportDialog()` method with confirmation
- Loading indicator during import
- Results dialog with success/error summary
- Imports `pole_import_util.dart`

## How It Works

### Data Flow

``` text
CSV File → pole_import_util.dart → PoleInventoryService → Firestore
```

### Import Process

1. User clicks import button (or runs CLI script)
2. CSV file is read and parsed into rows
3. Brand is auto-detected from row 2
4. Each row is processed in 3 column groups (starting at columns 2, 6, 10)
5. Length persists until new length appears in column group
6. Length is parsed into feet and inches
7. Barcode is generated from brand + serial (or brand + length + weight)
8. Pole is added via `PoleInventoryService.addPole()`
9. Results are collected and displayed

### CSV Structure

The BPVA Essx Inventory CSV has a unique format:

- Row 1: Headers (skipped)
- Row 2: Brand row (e.g., "ESSX Inventory")
- Row 3-4: More headers (skipped)
- Row 5+: Data rows with 3 column groups:
  - **Group 0 (cols 2-4):** Length, Weight, Serial #
  - **Group 1 (cols 6-8):** Length, Weight, Serial #
  - **Group 2 (cols 10-12):** Length, Weight, Serial #
  - Length persists across rows until a new length is specified

## Usage Examples

### In-App Import (Recommended)

1. Navigate to Pole Inventory screen
2. Click upload icon (📤) in top-right
3. Confirm import
4. View results

### CLI Import

1. Edit `lib/scripts/import_poles.dart`
2. Set `adminUserId = 'your-firebase-uid'`
3. Run: `flutter run lib/scripts/import_poles.dart`
4. Check console output

## Key Features

✅ **Automatic brand detection** from CSV row 2
✅ **Automatic barcode generation** (brand + serial or brand + length + weight)
✅ **Duplicate detection** (skips existing barcodes)
✅ **Error handling** with detailed reporting
✅ **Batch processing** with progress feedback
✅ **Admin/coach only** access control
✅ **Flexible CSV parsing** for multi-column format with persistent length tracking
✅ **Serial number preservation** from manufacturer data

## Testing Checklist

- [ ] Install csv package: `flutter pub get`
- [ ] Place CSV file in `assets/import-data/`
- [ ] Test in-app import as admin
- [ ] Verify poles appear in inventory
- [ ] Check barcodes are unique
- [ ] Test error handling with invalid CSV
- [ ] Verify duplicate detection works
- [ ] Test CLI script (optional)

## Next Steps

To use the import feature:

1. **Prepare CSV file**
   - Export from Numbers/Excel as CSV
   - Ensure row 2 contains "{BRAND} Inventory" (e.g., "ESSX Inventory")
   - CSV should have 3 column groups at positions 2, 6, 10 with Length, Weight, Serial #

2. **In-App Import (Recommended)**
   - Build and run app
   - Login as admin or BPVA coach
   - Go to Pole Inventory → Click upload icon (📤)
   - Select your CSV file using file picker
   - Confirm and review results

3. **CLI Import (Alternative)**
   - Place CSV file in `assets/import-data/BPVA Essx Inventory.csv`
   - Edit `lib/scripts/import_poles.dart` and set your admin user ID
   - Run: `flutter run lib/scripts/import_poles.dart`
   - Check console output

4. **Verify results**
   - Check Inventory tab for new poles
   - Verify barcodes are unique (format: POLE-{brand}-{serial})
   - Check that serial numbers are preserved

## Customization

### Change Brand Auto-Detection

Brand is automatically detected from row 2 of the CSV file. The format should be:
`"{BRAND} Inventory"` (e.g., "ESSX Inventory", "UCS Inventory", "Pacer Inventory")

To modify brand normalization logic, edit `pole_import_util.dart` lines 32-43.

### Change CSV File Selection

The import feature uses `FilePicker` to let users select any CSV file. No hardcoded path needed. Users simply:

1. Click the import button
2. Browse to select their CSV file
3. Confirm import

### Custom CSV Format

Modify `pole_import_util.dart` method `importFromCsv()` to adjust:

- Column indices
- Row start index
- Column group size
- Parsing logic

## Security

- Only admins and BPVA coaches can access import
- All imports logged with `createdBy` field
- Firestore rules enforce write permissions
- No sensitive data in CSV (just serial numbers)

## Performance

- **Sequential processing** (not parallel)
- **Typical speed**: Fast processing, limited by Firestore write speed
- **Firebase limits**: Multiple writes executed as fast as possible
- **Barcode uniqueness**: Based on brand + serial number (or length + weight)

## Error Scenarios Handled

- Invalid length format
- Invalid weight format
- Missing serial numbers
- Duplicate barcodes
- Empty CSV rows
- File not found
- Permission denied
- Firebase errors

All errors are collected and displayed with details.
