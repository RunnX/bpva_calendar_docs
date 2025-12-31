---
layout: default
title: Gym Check-In
description: Record your attendance at the gym with location verification
---

# Gym Check-In

## Overview

The Gym Check-In feature allows athletes to record their attendance when arriving at BPVA for practice. Using GPS location verification, the app ensures you're actually at the gym before allowing check-in. This helps track attendance and training frequency.

## Features

### Location Verification
- **GPS-Based Check-In**: Verify you're at the gym using your device's location
- **Proximity Detection**: Must be within allowed radius (typically 100 meters)
- **Real-Time Location**: Check your current distance from gym
- **Automatic Validation**: App confirms you're at the correct location

### Check-In Limits
- **Once Per Day**: Can only check in once per day
- **Midnight Reset**: Check-in limit resets at midnight
- **Prevents Abuse**: Ensures accurate attendance tracking
- **Fair Usage**: Same rules for all athletes

### History Tracking
- **Check-In History**: View your past check-ins
- **Lifetime Stats**: See total number of gym visits
- **Monthly Stats**: Track this month's attendance
- **Weekly Stats**: Monitor weekly check-ins
- **Date Tracking**: See when you checked in

## How to Use

### First-Time Setup

1. **Enable Location Services**
   - When prompted, allow location access
   - App needs location "While Using"
   - Required for check-in feature

2. **Grant Permission**
   
   **iOS:**
   - Tap **"Allow While Using App"** when prompted
   - Or: Settings → BPVA Calendar → Location → While Using App
   
   **Android:**
   - Tap **"Allow"** when prompted
   - Or: Settings → Apps → BPVA Calendar → Permissions → Location → Allow

### Checking In

1. **Navigate to Gym Check-In**
   - Open app at the gym
   - Tap hamburger menu (☰)
   - Select **"Gym Check-In"**

2. **Check Your Location (Optional)**
   - Tap **"Check Location"** button
   - App shows your distance from gym
   - Confirms if you're in range
   - ✅ "You are at the gym location!" = Ready to check in
   - ❌ "You are not at the gym location" = Too far away

3. **Complete Check-In**
   - Tap **"Check In"** button (green)
   - Wait for location verification
   - Success message appears: "Check-in Successful! 🎉"
   - Confirmation shows check-in time and distance

4. **View Confirmation**
   - Success dialog shows:
     - Check-in time
     - Distance from gym (in meters)
     - Message confirming successful check-in
   - Tap **OK** to close

### Viewing History

1. **Check-In Statistics**
   - View current stats on main check-in screen:
     - **Today**: Check-ins today (0 or 1)
     - **This Week**: Total check-ins this week
     - **This Month**: Total check-ins this month
     - **Lifetime**: All-time check-in count

2. **Detailed History**
   - Scroll down on check-in screen
   - View list of recent check-ins
   - See date and time of each check-in
   - Review your attendance patterns

## Understanding the Check-In Screen

### Header Information
- **Gym Name**: "BPVA Gym Check-In"
- **Status Message**: Current check-in status
- **Last Check-In**: Time of your most recent check-in
- **Next Check-In**: When you can check in again (if within 24 hours)

### Action Buttons

**Check Location** (Blue):
- Verifies your current distance from gym
- Shows real-time location status
- Does not record a check-in
- Use to confirm you're in range

**Check In** (Green):
- Records your gym attendance
- Only enabled when:
  - Location permission granted
  - You're within gym radius
  - You haven't checked in today
- Disabled if already checked in today

### Status Messages

**Location Messages:**
- "✅ You are at the gym location!" - Ready to check in
- "❌ You are not at the gym location" - Too far from gym
- "Checking your location..." - GPS is determining position
- "Location permission required" - Need to grant permission

**Check-In Messages:**
- "Checking you in..." - Recording attendance
- "Check-in successful!" - Completed successfully
- "You've already checked in today" - Daily limit reached
- "You can check in again at [time]" - When next check-in available

## Location Verification Details

### How It Works
1. App requests your current GPS location
2. Calculates distance from gym coordinates
3. Compares distance to allowed radius (100m typically)
4. If within radius, check-in is allowed
5. Records check-in with timestamp and distance

### Privacy & Data
- **Location Usage**: Only checked during check-in
- **No Tracking**: App doesn't track you continuously
- **Stored Data**: Only distance from gym, not exact location
- **Permission**: You control when location is accessed
- **Security**: User authentication required

### Accuracy
- **GPS Accuracy**: Typically within 5-15 meters
- **Building Effects**: Metal buildings can affect GPS signal
- **Weather**: Rain/clouds may slightly reduce accuracy
- **Position**: Near windows usually better than interior

## Troubleshooting

### Can't Check In - "Too Far Away"

**Verify You're at the Gym:**
- Physically confirm you're at BPVA facility
- Try moving closer to center of building
- Try near windows if inside

**Check GPS Signal:**
- Ensure location services enabled on device
- Wait a few moments for GPS to acquire signal
- Try "Check Location" button multiple times
- Move to area with better sky view

**Distance Issues:**
- If consistently too far while at gym:
  - Note your distance (shown in error message)
  - Contact coach to review gym radius settings
  - May need radius adjustment

### Location Permission Denied

**iOS:**
1. Open **Settings**
2. Scroll to **BPVA Calendar**
3. Tap **Location**
4. Select **"While Using App"**
5. Return to app and try again

**Android:**
1. Open **Settings**
2. Navigate to **Apps** → **BPVA Calendar**
3. Tap **Permissions**
4. Tap **Location**
5. Select **"Allow only while using the app"**
6. Return to app and try again

### Already Checked In Today

**Once-Per-Day Limit:**
- Check-in limit: once every 24 hours
- Wait until midnight for reset
- Status message shows when you can check in again
- Prevents accidental duplicate check-ins

**Time Zones:**
- Reset based on device time zone
- Traveling may affect reset time
- Check-in time shown in local time

### Location Services Disabled

**Enable Location Services:**

**iOS:**
1. **Settings** → **Privacy & Security** → **Location Services**
2. Enable **Location Services** (top toggle)
3. Scroll to **BPVA Calendar**
4. Select **"While Using App"**

**Android:**
1. **Settings** → **Location**
2. Enable location (toggle ON)
3. Ensure **High Accuracy** mode selected
4. Return to app

### GPS Not Acquiring Position

**Improving GPS Signal:**
- Move near windows
- Go outside temporarily
- Wait 30-60 seconds for GPS lock
- Restart app if needed
- Enable "High Accuracy" location mode

**Device Issues:**
- Restart your device
- Update to latest OS version
- Check if other GPS apps work
- May be hardware issue if persistent

## Best Practices

### Successful Check-Ins
- **Arrive Early**: Check in when you first arrive
- **Stable Connection**: Wait for GPS to stabilize
- **Windows/Outside**: Better GPS signal
- **Patient**: Give GPS time to acquire signal
- **Daily Limit**: Remember once-per-day rule

### Tracking Attendance
- **Consistency**: Check in every practice
- **Accountability**: Track your training frequency
- **Goals**: Set attendance goals (e.g., 3x/week)
- **Review**: Check your statistics regularly
- **Motivation**: Use stats to stay motivated

### Privacy
- **Understand Permissions**: Know what location access allows
- **Revoke Anytime**: Disable location if desired
- **Limited Usage**: Only checked during check-in
- **No Sharing**: Your exact location isn't shared

## Check-In Rules

### Requirements
- Must be physically at gym
- Within 100 meters of gym location (typical)
- Location permission granted
- Signed into app
- Haven't checked in within last 24 hours

### Limits
- **Frequency**: Once per day
- **Time Window**: 24-hour period
- **Reset**: Midnight local time
- **Verification**: GPS location required

### Admin Visibility
- Coaches can see check-in statistics
- Attendance reports available to admins
- Helps track athlete participation
- Not used for disciplinary purposes

## Need Help?

### Common Questions

**Q: Why does it say I'm not at the gym when I am?**
A: Move near windows, wait for GPS signal, or contact coach if radius needs adjustment.

**Q: Can I check in multiple times per day?**
A: No, check-in is limited to once per 24 hours to ensure accurate tracking.

**Q: What if I forget to check in?**
A: Can't retroactively check in. Remember to check in when you arrive at practice.

**Q: Does this track me when I'm not at the gym?**
A: No, location is only checked when you use the check-in feature.

**Q: Can parents check in for athletes?**
A: No, athletes must check in using their own devices.

### Contact Support
If issues persist:
1. Screenshot error messages
2. Note your location and distance shown
3. Contact coach or admin
4. Provide device type and OS version

---

**Related Documentation:**
- [My Profile](profile.md)
- [Sign In / Sign Out](sign-in.md)
