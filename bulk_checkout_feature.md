# Bulk Pole Checkout/Checkin Feature

## Overview

The Bulk Checkout/Checkin feature allows admins and BPVA coaches to efficiently process multiple pole checkouts or checkins for a single athlete in one operation. This streamlines the workflow when athletes are renting or returning multiple poles simultaneously.

## Key Features

- **Dual Mode Operation**: Toggle between checkout and checkin modes
- **Athlete Selection**: Select the athlete upfront before scanning poles
- **Multi-Pole Scanning**: Scan multiple poles using QR code scanner to build a list
- **List Management**: Review and remove poles from the list before processing
- **Batch Processing**: Process all checkouts/checkins in a single operation
- **Error Handling**: Displays detailed success/failure summary for each pole
- **Smart Validation**: Only allows valid poles for each operation mode

## Access

**Who can use it:**

- Admins
- BPVA Coaches (role: `bpva-coach`)

**How to access:**

1. Navigate to **Admin** → **Pole Inventory**
2. On the **Inventory** or **Active Rentals** tab, tap the top floating action button (playlist icon) from the draggable FAB stack
   - The FAB stack contains three buttons: Bulk Checkout/Checkin, Quick Scan, and Add Pole
   - The FAB can be dragged to any position on screen
   - The FAB is hidden on the History tab

## User Workflow

### Checkout Mode (Default)

1. **Launch Screen**: Tap the bulk checkout button (top button in FAB stack)
2. **Select Athlete**: Choose the athlete from the dropdown
3. **Set Return Date** (Optional): Tap to select expected return date
4. **Scan Poles**:
   - Tap "Scan Pole" button
   - Scan pole's QR code/barcode
   - Pole appears in the scanned list
   - Repeat for all poles to checkout
5. **Review List**: Check the scanned poles list
   - Remove any poles by tapping the red X icon
6. **Process Checkout**: Tap "Checkout X Poles" button
7. **View Results**: Review success/failure summary dialog

### Checkin Mode

1. **Switch Mode**: Tap the toggle icon in the app bar (switches from logout to login icon)
2. **Select Athlete**: Choose the athlete returning poles
3. **Scan Poles**:
   - Tap "Scan Pole" button
   - Scan pole's QR code/barcode
   - Only poles currently checked out to the athlete will be added
4. **Review List**: Check the scanned poles list
5. **Process Checkin**: Tap "Checkin X Poles" button
6. **View Results**: Review success/failure summary dialog

## Validation Rules

### Checkout Mode Validations

- **Pole Status**: Only `available` poles can be added to checkout list
- **Duplicate Prevention**: Cannot add the same pole twice
- **Athlete Selection**: Must select an athlete before processing

### Checkin Mode Validations

- **Pole Status**: Only `checkedOut` poles can be added to checkin list
- **Active Checkout**: Pole must have an active checkout record
- **Duplicate Prevention**: Cannot add the same pole twice

### Error Messages

| Scenario | Message |
|----------|---------|
| Pole not found | "Pole not found with barcode: {barcode}" |
| Already in list | "{brand} {weight}lbs already added" |
| Wrong status (checkout) | "{brand} {weight}lbs is {status}, not available for checkout" |
| Wrong status (checkin) | "{brand} {weight}lbs is {status}, not checked out" |
| No athlete selected | "Please select an athlete" |
| No poles scanned | "Please scan at least one pole" |

## Technical Implementation

### Files Created/Modified

**New File:**

- `lib/screens/admin/bulk_checkout_screen.dart` - Main bulk checkout/checkin screen

**Modified Files:**

- `lib/screens/admin/pole_inventory_screen.dart` - Added draggable FAB with bulk checkout button, History tab export/clear features, and "Check In" menu option
- `lib/services/pole_inventory_service.dart` - Added `deleteCheckoutRecord()` method
- `lib/utils/checkout_history_pdf_export_util.dart` - New PDF export utility for checkout history
- `firestore.rules` - Updated to allow admins and BPVA coaches to delete checkout records

### Data Flow

```text
1. User scans pole barcode
   ↓
2. BulkCheckoutScreen queries PoleInventoryService.getPoleByBarcode()
   ↓
3. Validates pole status matches operation mode
   ↓
4. Adds pole to scanned list (in-memory)
   ↓
5. User taps "Checkout/Checkin X Poles" button
   ↓
6. For each pole in list:
   - Checkout mode: PoleInventoryService.checkoutPole()
   - Checkin mode: PoleInventoryService.returnPole()
   ↓
7. Collects success/error results
   ↓
8. Displays summary dialog
```

### Batch Processing Logic

```dart
for (final pole in _scannedPoles) {
  try {
    if (_isCheckout) {
      await _poleInventoryService.checkoutPole(
        poleId: pole.id,
        athleteId: _selectedUserId!,
        athleteName: member.displayName,
        athleteEmail: member.email,
        adminUserId: currentUser.id,
        expectedReturnDate: _expectedReturnDate,
        notes: 'Bulk checkout',
      );
    } else {
      // Find active checkout and return pole
      await _poleInventoryService.returnPole(
        checkoutId: activeCheckout.id,
        adminUserId: currentUser.id,
        notes: 'Bulk checkin',
      );
    }
    successCount++;
  } catch (e) {
    errorCount++;
    errors.add('${pole.brand} ${pole.weight}lbs: $e');
  }
}
```

## UI Components

### Mode Indicator Card

- **Blue background** (checkout mode): Shows "Checkout Mode" with logout icon
- **Green background** (checkin mode): Shows "Checkin Mode" with login icon

### Athlete Dropdown

- Filtered to show only athletes (`isAthlete: true`)
- Displays athlete's display name
- Required field with validation

### Return Date Selector (Checkout Only)

- Optional date picker
- Defaults to 7 days from today
- Range: Today to 1 year from today

### Scanned Poles List

- Empty state: Shows scanner icon with "No poles scanned yet" message
- Populated state: Card with list of poles
  - Pole brand, length, weight, flex (if available)
  - Barcode displayed
  - Remove button (red X icon)
- Color-coded icons: Blue (checkout) / Green (checkin)

### Action Buttons

- **Cancel**: Returns to pole inventory screen
- **Checkout/Checkin X Poles**:
  - Shows count of scanned poles
  - Disabled during processing (shows spinner)
  - Changes text based on mode

## Results Summary Dialog

**Title:** "Bulk Checkout Complete" or "Bulk Checkin Complete"

**Content:**

- ✅ Success count with green text
- ❌ Error count with red text (if any)
- Detailed error list:
  - Pole description (brand, weight)
  - Error message for each failed operation

**Behavior:**

- If all poles succeed: Closes dialog and returns to inventory screen
- If any errors: Dialog stays open; user can review errors and close manually

## Example Use Cases

### Use Case 1: New Athlete Rental

**Scenario:** Athlete renting 3 poles for the season

1. Admin opens Bulk Checkout screen
2. Selects athlete "John Smith"
3. Sets return date to end of season (3 months)
4. Scans 3 pole QR codes
5. Reviews list: Essx 12'6" 130lbs, Essx 13'0" 140lbs, Gill 13'0" 135lbs
6. Taps "Checkout 3 Poles"
7. All succeed - auto-closes and returns to inventory

### Use Case 2: End of Practice Returns

**Scenario:** Athlete returning borrowed poles after practice

1. Admin opens Bulk Checkout screen
2. Taps mode toggle to switch to Checkin Mode
3. Selects athlete "Jane Doe"
4. Scans 2 pole QR codes
5. Reviews list: UCS 12'0" 125lbs, UCS 12'6" 130lbs
6. Taps "Checkin 2 Poles"
7. Both succeed - returns to inventory

### Use Case 3: Partial Success

**Scenario:** Mixed results during checkout

1. Admin scans 4 poles for checkout
2. 3 poles are available
3. 1 pole was already checked out
4. Processes checkout
5. Summary shows:
   - ✅ 3 successfully checked out
   - ❌ 1 error: "Essx 13'0" 140lbs is checkedOut, not available for checkout"
6. Admin reviews errors and investigates the unavailable pole

## Benefits Over Single-Pole Checkout

| Feature | Single (Quick Scan) | Bulk Checkout |
|---------|---------------------|---------------|
| Athlete selection | After scanning | Before scanning |
| Multiple poles | Scan → checkout → repeat | Scan all → checkout once |
| Return date | Set per pole | Set once for all |
| Error review | Immediate interrupt | Summary at end |
| Workflow efficiency | Good for 1 pole | Excellent for 3+ poles |

## Related Features

- **Quick Scan**: Single-pole checkout/checkin workflow (existing)
- **Pole Inventory**: Main inventory management screen with three tabs:
  - **Inventory Tab**: Browse and manage all poles, filterable by status/brand/length/weight
  - **Active Rentals Tab**: View all currently checked out poles
  - **History Tab**: View complete checkout history with Export to PDF and Clear History buttons
- **Check-In Menu Option**: Three-dot menu on checked out poles includes "Check In" option for quick returns
- **Draggable FAB**: Movable floating action button stack with Bulk Checkout, Quick Scan, and Add Pole buttons
- **QR Code Scanner**: `BarcodeScannerWidget` for barcode scanning
- **Pole Checkout Service**: `PoleInventoryService` handles all checkout/checkin operations
- **PDF Export**: `CheckoutHistoryPdfExportUtil` exports history to landscape PDF table format (20 items/page)

## Future Enhancements (Potential)

- [ ] Support for bulk operations across multiple athletes
- [ ] Save "common kits" (predefined pole sets for specific athletes)
- [ ] Bulk edit return dates for active checkouts
- [ ] Export bulk checkout/checkin records to CSV
- [ ] Barcode multi-scan mode (continuous scanning without navigation)
- [ ] Undo last bulk operation

## Testing Recommendations

### Manual Testing Checklist

- [ ] Checkout mode: Scan and checkout 3+ available poles
- [ ] Checkin mode: Scan and checkin 3+ checked out poles
- [ ] Mode toggle: Switch between checkout/checkin modes
- [ ] Validation: Try scanning unavailable pole in checkout mode
- [ ] Validation: Try scanning available pole in checkin mode
- [ ] Duplicate prevention: Scan same pole twice
- [ ] Remove pole: Add pole then remove from list
- [ ] Empty list: Try processing with no poles scanned
- [ ] No athlete: Try processing without selecting athlete
- [ ] Partial success: Deliberately create mixed success/failure scenario
- [ ] Cancel operation: Test cancel button at various stages

### Unit Test Coverage

**`BulkCheckoutScreen` widget tests:**

- Athlete dropdown population
- Mode toggle functionality
- Date picker interaction
- Pole scanning flow
- List management (add/remove)
- Form validation
- Batch processing logic
- Error handling
- Results dialog display

**Integration tests:**

- Full checkout workflow with mock `PoleInventoryService`
- Full checkin workflow with mock data
- Error scenarios with mock failures

## Firestore Impact

### Collections Modified

**`/poles/{poleId}`**

- Status updated: `available` → `checkedOut` (on checkout)
- Status updated: `checkedOut` → `available` (on checkin)

**`/pole_checkouts/{checkoutId}`** (new documents)

- Creates new checkout document for each pole
- Fields: `poleId`, `athleteId`, `athleteName`, `athleteEmail`, `checkoutDate`, `adminUserId`, `expectedReturnDate`, `notes`, `status`

**`/pole_checkouts/{checkoutId}`** (updates)

- Updates `returnDate`, `returnedByAdminId`, `returnNotes`, `status` on checkin

### Write Operations

- **Checkout**: 2 writes per pole (1 pole update + 1 checkout create)
- **Checkin**: 2 writes per pole (1 pole update + 1 checkout update)
- **Example**: Checking out 5 poles = 10 Firestore writes

## Notes for Developers

1. **Singleton Services**: The screen uses injected services or falls back to singleton instances
2. **Test Constructors**: Service dependencies can be mocked for testing
3. **Error Collection**: Batch processing collects all errors rather than stopping on first failure
4. **Notes Field**: Hardcoded to "Bulk checkout" / "Bulk checkin" to distinguish from single-pole operations
5. **Active Checkout Query**: For checkin, queries athlete's checkout history to find active checkout for each pole
6. **Navigator Context**: Careful handling of dialog contexts to avoid "BuildContext across async gaps" issues

## Version History

- **v1.0** (2025-11-27): Initial implementation with dual-mode support
  - Bulk checkout/checkin screen
  - Draggable FAB on pole inventory screen
  - Checkout history PDF export feature
  - Clear history feature with confirmation
  - "Check In" option in three-dot menu for checked out poles
  - Tab-based visibility for FAB (hidden on History tab)
