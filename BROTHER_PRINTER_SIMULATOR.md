# Brother Printer SDK - iOS Simulator Limitation

## Problem
The Brother SDK (`BRLMPrinterKit` and `BROTHERSDK`) frameworks are **device-only** and do not support iOS simulators. They only contain ARM64 slices for physical iOS devices.

## Solution: Conditional Compilation

We've implemented conditional compilation at the Dart level:

### File Structure
- `lib/utils/brother_printer_util.dart` - Main entry point with conditional exports
- `lib/utils/brother_printer_util_device.dart` - Full implementation with Brother SDK (for devices)
- `lib/utils/brother_printer_util_stub.dart` - Stub implementation (for simulators/web)

### How It Works
1. **Web builds**: Automatically use the stub (via `dart.library.html` conditional)
2. **iOS/Android device builds**: Use the full device implementation with Brother SDK
3. **iOS Simulator builds**: Currently fail due to native framework linking

## iOS Simulator Workaround

### Option 1: Test on Physical Device (Recommended)
```bash
# List available devices
./run.sh dev ios list

# Run on physical iPad
./run.sh dev ios 00008112-001C20911E68A01E
```

### Option 2: Temporarily Disable Brother Printer for Simulator Testing

To test other features on iOS simulator, temporarily comment out `another_brother` in `pubspec.yaml`:

```yaml
dependencies:
  # another_brother: ^2.2.4  # Comment out for simulator builds
```

Then:
```bash
flutter clean
flutter pub get
cd ios && pod install && cd ..
flutter run --flavor dev -d "iPhone Air"
```

**Remember to uncomment before building for physical devices or production!**

### Option 3: Use Build Flavors (Future Enhancement)
Create separate flavor configurations with different dependencies, but this requires maintaining multiple pubspec files.

## Testing Strategy

### Features That Work on Simulator
✅ Calendar views and event management
✅ Gym check-in (with location mocking)
✅ QR code scanning (via mobile_scanner)
✅ PDF export (via file_picker)
✅ User management and authentication
✅ All Firebase features

### Features That Require Physical Device
❌ Brother label printer connectivity
❌ Actual Brother printer label printing

## For Future Development

If Brother releases simulator-compatible frameworks, update the Podfile to remove the device-only restrictions and the conditional compilation will automatically work.

## Implementation Notes

The conditional compilation is implemented using Dart's conditional imports:
- `export 'file_a.dart' if (dart.library.html) 'file_b.dart'`
- This allows compile-time selection of implementation
- No runtime overhead
- Type-safe (both files export the same public API)

The stub implementation shows user-friendly messages when printer functions are called on unsupported platforms.
