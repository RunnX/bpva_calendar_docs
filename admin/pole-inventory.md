# Pole Inventory Management System

## Overview

The Pole Inventory Management System is a comprehensive feature for tracking pole vault poles and managing athlete rentals at BPVA (Buckeye Pole Vault Academy). This system allows administrators and BPVA coaches to maintain an inventory of poles, rent them out to athletes, and track returns.

## Features

### 1. Pole Inventory Management

- **Add Poles**: Create new pole entries with brand, weight, length, barcode, and optional purchase date
- **Edit Poles**: Update pole information and change status (Available, Checked Out, Maintenance, Retired)
- **Delete Poles**: Remove poles from inventory (with validation to prevent deletion of checked-out poles)
- **View Inventory**: Real-time list of all poles grouped by status
- **QR Codes**: Each pole has a unique QR code for easy identification

### 2. Barcode Scanning & Labels

- **Barcode Generation**: Auto-generate unique barcodes with timestamp (e.g., `POLE-1732204567890`)
- **Mobile Scanner**: Use device camera to scan existing pole barcodes during data entry
- **Printable QR Labels**: Display pole information with scannable QR code for label printing
- **Label Maker Compatible**: Print labels to attach to physical poles for easy identification
- **High Error Correction**: QR codes use high error correction level for reliable scanning even if damaged
- **Unique Identification**: Each pole has a unique barcode for tracking

### 3. Rental Management

- **Checkout Poles**: Rent poles to athletes with expected return dates
- **Quick Scan Checkout/Checkin**: Scan pole barcode to instantly checkout or checkin based on current status
- **Return Poles**: Mark poles as returned with optional notes
- **Overdue Tracking**: Visual highlighting of overdue rentals
- **Rental History**: Complete history of all pole checkouts and returns

### 4. Role-Based Access

- **Navigation**: Access via "Pole Inventory" menu item in admin dashboard, athlete menu, or parent menu
- **Admin & BPVA Coach Access**: Full inventory management including add, edit, delete, checkout, and checkin
- **Athlete Access**: View available poles and their own current rentals
- **Parent Access**: View poles rented by their linked athletes
- **External Coach Access**: View pole inventory and checkout details (read-only)
- **Real-Time Updates**: Firestore streams provide live inventory updates

## Data Models

### Pole

```dart
class Pole {
  final String id;
  final String brand;           // e.g., "Pacer", "UCS", "Gill"
  final double weight;          // Weight in pounds
  final double length;          // Calculated from lengthFeet + lengthInches/12
  final int lengthFeet;         // Length in feet (e.g., 13, 14, 15)
  final int lengthInches;       // Additional inches (0-11)
  final double? flex;           // Optional flex rating (e.g., 14.5, 15.0)
  final DateTime? purchaseDate; // Optional purchase date
  final String barcode;         // Unique identifier (e.g., POLE-1732204567890)
  final PoleStatus status;      // Available, CheckedOut, Maintenance, Retired
  final DateTime createdAt;
  final String createdBy;       // Admin user ID
}

enum PoleStatus {
  available,
  checkedOut,
  maintenance,
  retired,
}
```

### PoleCheckout

```dart
class PoleCheckout {
  final String id;
  final String poleId;
  final String athleteId;
  final String athleteName;
  final String athleteEmail;
  final DateTime checkoutDate;
  final DateTime? expectedReturnDate;
  final DateTime? actualReturnDate;
  final String checkedOutBy;    // Admin/receptionist user ID
  final String? checkedInBy;    // Admin/receptionist user ID
  final String? notes;
  final CheckoutStatus status;  // Active, Returned, Overdue
}

enum CheckoutStatus {
  active,
  returned,
  overdue,
}
```

## Firestore Collections

### `/poles/{poleId}`

Stores all pole inventory data.

**Security Rules:**

- Read: All authenticated users
- Write: Admins and BPVA coaches

**Indexed Fields:**

- `brand` + `weight` (compound index for sorting)

### `/pole_checkouts/{checkoutId}`

Stores all rental/checkout records.

**Security Rules:**

- Read: All authenticated users (can see which poles are checked out)
- Create/Update: Admins and BPVA coaches

**Privacy Protection:**

- All users can see which poles are checked out and when they're due
- Only admins and coaches (BPVA and external) can see who has checked out the poles
- Users can always see full details of their own checkouts
- Athlete names/emails are hidden from non-admin/non-coach users in the UI
- BPVA coaches have full management permissions (same as admins)
- External coaches can view athlete information but cannot manage inventory or process checkouts/returns

**Indexed Fields:**

- `status` + `checkoutDate` (compound index for active rentals)
- `athleteId` + `checkoutDate` (compound index for athlete history)

## Service Layer

### PoleInventoryService

**Methods:**

```dart
// Pole CRUD
Future<String> addPole({...})
Future<void> updatePole(Pole pole)
Future<void> deletePole(String poleId)
Stream<List<Pole>> getPoles()
Future<Pole?> getPoleByBarcode(String barcode)
Future<Pole?> getPoleById(String poleId)

// Checkout Operations
Future<String> checkoutPole({...})
Future<void> returnPole({...})
Stream<List<PoleCheckout>> getActiveCheckouts()
Stream<List<PoleCheckout>> getCheckoutHistory({String? athleteId})
Future<List<PoleCheckout>> getAthleteCheckouts(String athleteId)
```

## User Interface

### Pole Inventory Screen

Three-tab interface accessible from admin dashboard, athlete menu, or parent menu:

1. **Inventory Tab**
   - Grouped display by pole status
   - Pole cards showing brand, weight, length (feet'inches"), and optional flex rating
   - "Show QR Code" menu option displays printable label for all users
   - Action buttons (admins & BPVA coaches only): Rent Out, Edit, Delete
   - Floating action buttons (admins & BPVA coaches only):
     - **Quick Scan** (top): Scan pole barcode for instant checkout/checkin
     - **Add Pole** (bottom): Add new poles to inventory

2. **Active Rentals Tab**
   - List of currently rented poles
   - Overdue highlighting (red background)
   - Athlete information (visible to admins, BPVA coaches, and external coaches)
   - Return button for each rental (admins & BPVA coaches only)

3. **History Tab**
   - Complete rental history
   - Status indicators
   - Searchable/filterable (future enhancement)

### Dialogs

- **Add Pole Dialog**: Form with brand, weight, length (feet & inches), optional flex rating, barcode (with scanner and auto-generate button), optional purchase date
- **Edit Pole Dialog**: Update pole details, length (feet & inches), flex rating, and status
- **Printable Label Dialog**: Display QR code with pole details for printing on label maker
- **Quick Scan Dialog**: Camera scanner to scan pole barcode, automatically routes to checkout or checkin
- **Quick Checkout Dialog**: Fast checkout workflow showing pole info card, athlete selection (athletes only), and optional return date
- **Quick Checkin Confirmation**: Fast checkin showing pole details, checkout info, and one-click return
- **Checkout Dialog**: Select athlete (athletes only), set return date, add notes
- **Return Dialog**: Confirm return with optional notes
- **Delete Confirmation**: Prevent accidental deletions

## Barcode & Label System

### Barcode Format

Barcodes are auto-generated with format: `POLE-{timestamp}`

Example: `POLE-1732204567890`

This ensures uniqueness through the timestamp while maintaining a clear "POLE" prefix for easy identification.

### Printable Labels

The "Show QR Code" feature displays a printable label containing:
- Pole description (brand, weight, length, flex)
- QR code encoding the barcode
- Human-readable barcode text

**Printing Instructions:**
1. Open the pole's QR code dialog
2. Take a screenshot or use browser print function
3. Print on label maker or regular printer
4. Cut to size and attach to physical pole

**Label Maker Compatibility:**
- Works with any QR-capable label printer
- Standard barcode scanners can read QR codes
- Mobile devices can scan with camera app
- High error correction allows scanning even if partially damaged

### BarcodeScannerWidget

- Uses `mobile_scanner` package (v5.2.3)
- Camera-based barcode/QR code scanning
- Torch/flashlight control
- Camera flip functionality
- Auto-dismiss on successful scan

### QR Code Display

- Uses `qr_flutter` package (v4.1.0)
- Generates QR codes encoding the pole barcode
- High error correction level (H) for durability
- White background for printing
- Displayed in printable label dialog
- Can be printed for physical pole labels

## Setup Instructions

### 1. Install Dependencies

Already added to `pubspec.yaml`:

```yaml
dependencies:
  mobile_scanner: ^5.2.3
  qr_flutter: ^4.1.0
```

Run: `flutter pub get`

### 2. Deploy Firestore Rules

The security rules have been updated in `firestore.rules`. Deploy with:

```bash
firebase deploy --only firestore:rules
```

### 3. Deploy Firestore Indexes

The indexes have been added to `firestore.indexes.json`. Deploy with:

```bash
firebase deploy --only firestore:indexes
```

### 4. Camera Permissions

**iOS (`ios/Runner/Info.plist`):**
Add camera permission:

```xml
<key>NSCameraUsageDescription</key>
<string>This app needs camera access to scan pole barcodes</string>
```

**Android (`android/app/src/main/AndroidManifest.xml`):**
Add camera permission:

```xml
<uses-permission android:name="android.permission.CAMERA"/>
```

## Usage Workflow

### Quick Scan Checkout/Checkin (Admins & BPVA Coaches)

1. Navigate to Pole Inventory → Inventory tab
2. Tap the **Quick Scan** floating action button (scanner icon, top position)
3. Scan the pole's barcode/QR code with your camera
4. **If pole is available**:
   - Quick checkout dialog appears showing pole details
   - Select athlete from dropdown (filtered to athletes only)
   - Optionally set expected return date
   - Tap "Checkout" to complete
5. **If pole is checked out**:
   - Quick checkin confirmation appears showing pole and checkout details
   - Review athlete name, checkout date, and due date
   - Tap "Check In" to return the pole
6. Process completes with automatic note: "Quick checkout via barcode scan" or "Quick check-in via barcode scan"

### Adding a Pole (Admins & BPVA Coaches)

1. Navigate to Admin Dashboard → Pole Inventory
2. Tap the **Add Pole** floating action button (plus icon, bottom position)
3. Fill in pole details:
   - Brand (e.g., "Pacer")
   - Weight in pounds (e.g., 12.5)
   - Length in feet (e.g., 13) and inches (0-11)
   - Optional flex rating (e.g., 14.5)
4. For barcode:
   - **Option 1**: Tap "Generate barcode" to auto-create unique code
   - **Option 2**: Tap scan icon to scan existing barcode
   - **Option 3**: Manually enter barcode
5. Optionally set purchase date
6. Tap "Add"
7. Use "Show QR Code" to print a label for the physical pole

### Renting Out a Pole (Admins & BPVA Coaches)

1. Go to Inventory tab
2. Expand the pole card
3. Tap "Rent Out" button
4. Select the athlete from dropdown (filtered to athletes only)
5. Optionally set expected return date
6. Add any notes
7. Tap "Checkout"

### Returning a Pole (Admins & BPVA Coaches)

1. Go to Active Rentals tab
2. Find the rental record
3. Tap "Return" button
4. Optionally add return notes
5. Tap "Return Pole" to confirm

### Viewing History

1. Go to History tab
2. View all past rentals with status indicators
3. Filter by athlete (future enhancement)

## Testing

### Model Tests

- `test/models/pole_test.dart` - Tests for Pole model
- `test/models/pole_checkout_test.dart` - Tests for PoleCheckout model

Run tests:

```bash
flutter test test/models/pole_test.dart test/models/pole_checkout_test.dart
```

All 14 tests passing ✅

### Service Tests

Future enhancement: Add integration tests for PoleInventoryService

## Future Enhancements

1. **Search & Filter**
   - Search poles by brand, weight, or barcode
   - Filter checkouts by athlete or date range

2. **Analytics Dashboard**
   - Most rented poles
   - Average rental duration
   - Overdue statistics

3. **Notifications**
   - Remind athletes of upcoming return dates
   - Alert admins of overdue rentals

4. **Batch Operations**
   - Bulk barcode generation
   - Print multiple labels at once
   - Export inventory to CSV

5. **Damage Tracking**
   - Log pole condition on checkout/return
   - Track maintenance history

6. **Mobile App for Athletes**
   - View their current rentals
   - See rental history
   - Scan pole to view details

## Architecture

### Pattern: Service-Oriented with Singleton

- **Singleton Services**: PoleInventoryService follows the app's standard singleton pattern
- **Test-Friendly**: Services have `.test()` constructors for dependency injection
- **Stream-Based**: Real-time updates via Firestore streams
- **Error Handling**: Comprehensive try-catch blocks with user feedback

### Code Organization

``` text
lib/
├── models/
│   ├── pole.dart                    # Pole data model
│   └── pole_checkout.dart           # PoleCheckout data model
├── services/
│   └── pole_inventory_service.dart  # Business logic
├── screens/
│   └── admin/
│       └── pole_inventory_screen.dart  # Admin UI
└── widgets/
    └── barcode_scanner_widget.dart     # Reusable scanner

test/
└── models/
    ├── pole_test.dart
    └── pole_checkout_test.dart
```

## Dependencies

- **flutter**: SDK
- **mobile_scanner**: ^5.2.3 - Barcode/QR code scanning
- **qr_flutter**: ^4.1.0 - QR code generation
- **cloud_firestore**: ^6.0.3 - Database
- **firebase_auth**: ^6.1.1 - Authentication

## Support

For questions or issues with the pole inventory system:

1. Check Firestore console for data integrity
2. Review security rules if permission errors occur
3. Ensure camera permissions are granted on device
4. Check Firestore indexes are deployed

## Version History

- **v1.2.0** (2024-11-23): BPVA coach permissions and quick scan workflow
  - BPVA coaches now have full inventory management permissions (same as admins)
  - External coaches retain read-only access
  - Quick scan feature: Scan pole barcode to instantly checkout or checkin
  - Smart routing: Automatically determines checkout vs checkin based on pole status
  - Athlete-only filtering in checkout dropdowns
  - Menu access for athletes (view available poles and own rentals)
  - Menu access for parents (view poles rented by linked athletes)
  - Updated Firestore security rules for BPVA coach permissions
  - Dual floating action buttons: Quick Scan (top) and Add Pole (bottom)

- **v1.1.0** (2024-11-21): Enhanced barcode system
  - Printable QR code labels with pole details
  - Auto-generate unique barcodes with timestamp
  - Length stored as feet and inches (not decimal)
  - Optional flex rating field for poles
  - High error correction QR codes for durability
  
- **v1.0.0** (2024-11-21): Initial implementation
  - Basic CRUD operations for poles
  - Rental checkout/return workflow
  - Barcode scanning integration
  - QR code generation
  - Admin dashboard integration
  - Comprehensive testing
