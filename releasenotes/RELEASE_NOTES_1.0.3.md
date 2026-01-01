# Release Notes - Version 1.0.3 (Build 36)

## What's New in This Version

### 📱 Enhanced Help & Documentation System

- **Comprehensive In-App Help**: Athletes and parents now have access to detailed help documentation directly within the app
- **Interactive Documentation Viewer**: Browse feature guides, tutorials, and FAQs without leaving the app
- **Improved Navigation**: Easily find answers to common questions about gym check-ins, runway configurations, calendar events, and more
- **System UI Compatibility**: Documentation screens properly accommodate iOS system menus and safe areas

### 🖨️ Brother Printer Support (iOS & Android)

- **Direct Label Printing**: Print pole inventory labels directly to Brother P-Touch printers via Bluetooth
- **iOS-Specific Improvements**: 
  - Clear connection instructions for iOS users
  - Platform-aware printer discovery
  - Automatic retry with printer selection when connection established
  - Step-by-step guidance for Bluetooth pairing and connection
- **Android Support**: Seamless printer discovery and connection on Android devices
- **Batch Printing**: Print multiple pole labels in sequence with progress tracking

### 📊 Automated Reporting System

- **Daily Check-In Reports**: Coaches receive automated email reports with gym check-in statistics
- **Configurable Schedules**: Customize report timing and frequency
- **CSV & PDF Export**: Download detailed check-in reports in multiple formats
- **Multi-Environment Support**: Separate configurations for development and production

### 👤 Athlete Management Enhancements

- **Document Management**: Coaches can upload and manage athlete documents with camera capture
- **Archive Modes**: Better organization of active vs. archived athletes
- **Profile Completion Flow**: Guided process for completing athlete profiles
- **Lifetime Check-In Tracking**: View complete check-in history for each athlete
- **Duplicate User Detection**: Prevent duplicate athlete accounts

### 📝 Digital Registration System

- **Indoor Meet Registration**: Digital forms for indoor competition registration
- **Non-App Athlete Management**: Register and manage athletes who don't use the app
- **Manual Registration**: Coaches can register athletes on their behalf
- **Document Collection**: Collect required documents during registration process

### 🎯 Coach Features

- **Coach Check-In System**: Track coach attendance and hours
- **Enhanced Dashboard**: Improved admin navigation and controls
- **Custom Claims Authentication**: Secure role-based access for document uploads
- **College Athlete Support**: New membership type for college-level athletes

### 🛠️ Technical Improvements

- **Fixed Pole Inventory Cache**: Resolved issue where inventory screen showed "no poles" when navigating back
- **Automatic OAuth Refresh**: Seamless token refresh when opening the app
- **QR Code System Upgrade**: Migrated to flutter_zxing for better QR code performance
- **Dependency Updates**: Updated all packages to latest stable versions
- **Android 15 Compatibility**: Full support for Android 15 edge-to-edge display
- **Google Play Compliance**: Added 16 KB page size support for latest Android requirements

### 🔐 Security Updates

- **Guest Mode Removed**: Streamlined authentication flow
- **Enhanced Firestore Rules**: Improved security for coach check-ins and athlete data
- **Node-forge Security Patch**: Updated to version 1.3.3 to address vulnerabilities

### 🐛 Bug Fixes

- **Timezone DST Handling**: Fixed recurring event timezone issues
- **Barcode Generation**: Corrected pole barcode generation bug
- **Calendar Cache**: Improved cache invalidation for timezone-aware events
- **iOS Info.plist**: Resolved syntax errors for Brother printer support
- **Report Timing**: Fixed daily reports to show current day's check-ins

### 📅 Calendar System

- **Improved Event Caching**: Better performance and reliability
- **Timezone Awareness**: Accurate handling of daylight saving time changes
- **OAuth Integration**: Seamless Google Calendar synchronization

---

This update focuses on enhancing the coaching and athlete management experience while improving reliability and adding powerful new features for pole vault training facility operations.
