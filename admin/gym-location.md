# Gym Check-In Feature

## Overview
The gym check-in feature allows regular users (non-admin) to check into the gym when they physically arrive at the location. This feature uses GPS location verification to ensure users are actually at the gym premises.

## Features
- **Location Verification**: Users must be within 100 meters of the gym to check in
- **Time Limits**: Check-ins are limited to once per day to prevent spam
- **History Tracking**: All check-ins are recorded locally and in Firestore
- **Permission Handling**: Automatic location permission requests
- **Real-time Status**: Shows current location status and check-in eligibility

## How It Works

### For Users
1. Sign into the app with your Google account
2. Navigate to the side menu and select "Gym Check-In"
3. Grant location permissions when prompted
4. Use "Check Location" to verify you're at the gym
5. Use "Check In" to complete your gym check-in
6. View your check-in history in the same dialog

### For Administrators
To configure the gym location:
1. Open `lib/services/location_service.dart`
2. Update the `_gymLatitude` and `_gymLongitude` constants with the actual gym coordinates
3. Optionally adjust `_allowedDistanceMeters` (default: 100 meters)
4. Note: Check-ins are now limited to once per day (resets at midnight)

## Getting Gym Coordinates
1. Open Google Maps
2. Navigate to your gym location
3. Right-click on the exact location and select "What's here?"
4. Copy the latitude and longitude values from the popup

## Technical Details

### Dependencies Added
- `geolocator: ^13.0.1` - GPS location services
- `permission_handler: ^11.3.1` - Location permission management

### Permissions Required
- **Android**: `ACCESS_FINE_LOCATION`, `ACCESS_COARSE_LOCATION`
- **iOS**: `NSLocationWhenInUseUsageDescription`

### Data Storage
- **Local**: Last check-in time stored in SharedPreferences
- **Cloud**: Full check-in records stored in Firestore collection `gym_checkins`

### Firestore Schema
```json
{
  "userId": "user@example.com",
  "userName": "User Name",
  "timestamp": "2025-10-05T10:30:00Z",
  "location": {
    "latitude": 40.7128,
    "longitude": -74.0060,
    "accuracy": 5.0
  },
  "distanceFromGym": 25.5
}
```

## Security Considerations
- Location data is only used for gym proximity verification
- Check-in intervals prevent abuse
- User authentication required for all check-ins
- Location permissions requested only when needed

## Error Handling
- Location services disabled: Prompts user to enable
- Permission denied: Clear error message with instructions
- Network issues: Local check-in still works, syncs later
- Distance validation: Clear feedback on proximity status

## Testing
To test the feature:
1. Update gym coordinates to your current location
2. Build and run the app
3. Sign in and navigate to Gym Check-In
4. Test location verification and check-in flow

## Future Enhancements
- Admin dashboard for viewing all user check-ins
- Check-in streaks and achievements
- Integration with membership management
- QR code backup check-in method
- Geofencing for automatic notifications