---
layout: default
title: Gym Location Settings
description: Configure gym location for athlete check-ins
---

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

**Configuring Gym Location:**

1. **Access Gym Location Settings**
   - Navigate to Admin Dashboard
   - Tap "Gym Location Settings"
   - View current gym coordinates and check-in radius

2. **Update Gym Coordinates**
   - Tap "Edit Location"
   - Enter new latitude and longitude values
   - Adjust check-in radius (default: 100 meters)
   - Save changes

3. **Test Location**
   - Use "Test Location" button to verify coordinates
   - Confirm check-in radius is appropriate
   - Make adjustments if needed

**Note**: Check-ins are limited to once per day per athlete (resets at midnight)

## Finding Your Gym Coordinates

**Using Google Maps:**
1. Open Google Maps in your web browser
2. Navigate to your gym location
3. Right-click (or long-press on mobile) on the exact gym location
4. Select "What's here?" from the menu
5. The coordinates appear at the bottom of the screen
6. Copy the latitude (first number) and longitude (second number)

**Example**: `40.7128, -74.0060` means:
- Latitude: 40.7128
- Longitude: -74.0060

## Check-In Radius Settings

**Recommended Radius:**
- Small facility: 50-75 meters
- Medium facility: 100 meters (default)
- Large campus: 150-200 meters
- Outdoor facility: 100-150 meters

**Adjusting the Radius:**
- Smaller radius = more accurate, but may cause issues if GPS is imprecise
- Larger radius = more forgiving, but athletes might check in from parking lot
- Test with athletes before finalizing
- Consider building materials (metal roofs can affect GPS)

## Privacy & Security

**How Location Data is Used:**
- Location data is ONLY used to verify athlete is at the gym
- Check-in records store distance from gym, not exact athlete location
- Once-per-day limit prevents abuse
- Athletes must be signed in to check in
- Location permissions requested only when athlete uses check-in feature

**Data Retention:**
- Check-in records kept for attendance tracking and reports
- No continuous location tracking - only checked during check-in
- Athletes control when location is accessed

## Error Handling
- Location services disabled: Prompts user to enable
- Permission denied: Clear error message with instructions
- Network issues: Local check-in still works, syncs later
- Distance validation: Clear feedback on proximity status

## Troubleshooting

**Athlete Can't Check In - "Too Far Away":**
- Verify gym coordinates are correct
- Check if radius is too small
- Ask athlete to enable high-accuracy GPS
- Try from different location in gym (GPS signal varies)
- Consider increasing radius by 25-50 meters

**Athletes Checking In from Parking Lot:**
- Reduce check-in radius
- Explain check-in policy to athletes
- Monitor check-in distances in reports
- Address individual cases with athletes

**Location Permission Denied:**
- Athlete must grant location permission in app settings
- On iOS: Settings → BPVA Calendar → Location → While Using App
- On Android: Settings → Apps → BPVA Calendar → Permissions → Location → Allow

**GPS Signal Issues:**
- Metal buildings can interfere with GPS
- Have athletes check in near windows if possible
- Consider QR code backup option for problematic locations
- Test from multiple locations inside gym

## Best Practices

**Initial Setup:**
- Set coordinates to center of gym facility
- Start with 100-meter radius
- Test with several athletes
- Adjust based on feedback
- Document final settings

**Ongoing Management:**
- Review check-in reports weekly
- Monitor distances to identify issues
- Communicate check-in expectations to athletes
- Address problems promptly
- Update coordinates if gym moves or expands