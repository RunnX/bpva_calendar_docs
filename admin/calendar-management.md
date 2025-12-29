---
layout: default
title: Calendar Management
description: Configure Google Calendars and manage calendar access for BPVA users
---

# Calendar Management

## Overview

The Calendar Management system allows administrators to configure which Google Calendars are visible to all BPVA app users. This feature provides complete control over calendar access, allowing admins to select public calendars and add external calendars by email.

## Features

### Public Calendar Selection
- **View Available Calendars**: See all Google Calendars accessible from your admin account
- **Select Public Calendar**: Choose which calendar becomes the main public calendar visible to all users
- **One Public Calendar**: Only one calendar can be designated as "public" at a time
- **Real-time Sync**: Calendar selection syncs instantly to all users via Firestore

### External Calendar Integration
- **Add by Email**: Add external public calendars using the calendar email address
- **Multiple External Calendars**: Add as many external calendars as needed
- **Calendar Sharing Verification**: Check if external calendars are properly shared with your account
- **Remove External Calendars**: Delete external calendars from the list

### Calendar Sharing Status
- **Sharing Check**: Verify if calendars are properly shared with the app
- **Permission Validation**: Ensure calendars have correct read permissions
- **Troubleshooting**: Identify and fix calendar access issues

## How to Use

### Setting Up Public Calendar (First Time)

1. **Access Calendar Management**
   - Open the BPVA Calendar app as an admin
   - Navigate to Admin Dashboard (hamburger menu in top-left)
   - Tap **Configuration** expansion tile
   - Tap **"Manage Calendars"** option

2. **Authenticate with Google**
   - On first access, you'll be prompted to sign in with Google
   - Grant the app permission to access Google Calendar
   - This is a one-time setup per device

3. **Select Public Calendar**
   - View the list of public calendars from your Google account
   - Find the calendar you want to make public (e.g., "BPVA Practice Schedule")
   - Tap the **"Public"** icon (globe icon) next to that calendar
   - Confirm the selection by tapping **"Save"** at the bottom

4. **Verify Configuration**
   - The selected calendar will show as "Public Calendar (Available to everyone)" in green text
   - All app users can now view events from this calendar
   - Events sync in real-time

### Adding External Calendars

External calendars allow you to display events from calendars owned by other Google accounts (e.g., a coach's personal calendar with meet schedules).

1. **Get Calendar Email**
   - Have the external calendar owner share their calendar's email address
   - Format: `calendarid@group.calendar.google.com` or personal email
   - Calendar must be set to "Public" or shared with your admin Google account

2. **Add External Calendar**
   - In **Manage Calendars** screen, scroll to "External Calendars" card
   - Tap **"Add External Public Calendar"** button
   - Enter the calendar email address in the text field
   - Tap **"Add Calendar"** button
   - The calendar will be validated and added if accessible

3. **Verify Sharing**
   - Tap **"Check Public Calendar Sharing"** button at the top of the screen
   - If properly shared, you'll see sharing status details
   - If not shared, you'll receive instructions to fix permissions

4. **Troubleshooting External Calendars**
   - If "Error accessing calendar" message appears, verify the calendar is actually shared
   - Check that the email address is exactly correct (including @group.calendar.google.com suffix)
   - Ensure the external calendar owner has set proper sharing permissions
   - Remove and re-add the calendar if issues persist

### Changing the Public Calendar

1. Navigate to **Manage Calendars** (Admin Dashboard → Configuration → Manage Calendars)
2. Find the new calendar you want to make public
3. Tap the **globe icon** (public icon) on the new calendar
4. Tap **"Save"** button at the bottom
5. The previous public calendar automatically becomes private
6. All users see events from the new public calendar immediately

### Removing External Calendars

1. In **Manage Calendars**, scroll to the "External Calendars" card
2. Find the external calendar you want to remove in the list
3. Tap the **delete/remove icon** next to the calendar
4. Confirm deletion
5. The calendar no longer appears for any users

## Calendar Data Flow

### Public Calendar
```
Admin Selects Calendar → Stored in Firestore (app_settings/public_calendar) → 
All Users Query Firestore → App Fetches Events → Display in Calendar View
```

### External Calendars
```
Admin Adds Calendar Email → Stored in Firestore (admin_settings/{email}/external_calendars) →
All Users Query External Calendars → App Fetches Events → Merge with Public Events → Display
```

## Technical Details

### Firestore Structure

**Admin Settings** (`admin_settings/{adminEmail}`)
```json
{
  "selectedCalendarId": "primary",
  "selectedCalendarName": "BPVA Practice Schedule",
  "lastUpdated": "2024-01-15T10:30:00Z",
  "external_calendars": {
    "calendarId": {
      "calendarEmail": "meets@example.com",
      "calendarName": "Regional Meets",
      "addedAt": "2024-01-15T11:00:00Z",
      "isShared": true
    }
  }
}
```

**Public Calendar Configuration** (`app_settings/public_calendar`)
```json
{
  "publicCalendarId": "primary",
  "publicCalendarName": "BPVA Practice Schedule",
  "lastUpdated": "2024-01-15T10:30:00Z"
}
```

### Google Calendar API

**Required Scopes:**
- `https://www.googleapis.com/auth/calendar.readonly` - Read calendar events
- `https://www.googleapis.com/auth/calendar.calendars.readonly` - List calendars

**Key Services:**
- `CalendarService` - Manages calendar configuration and event fetching
- `OAuthService` - Handles Google authentication and token management
- `FirestoreService` - Stores calendar settings for all users to access

### Event Synchronization

- **Real-time Updates**: Firestore listeners notify all users when calendar configuration changes
- **Event Caching**: Calendar events are cached locally to reduce API calls
- **Offline Support**: Cached events available even without internet
- **Auto-Refresh**: Events refresh every 15 minutes or when app comes to foreground

## Best Practices

### Calendar Organization
1. **Use Descriptive Names**: Name calendars clearly (e.g., "BPVA Practice Schedule" not "Calendar 1")
2. **One Source of Truth**: Keep the main practice schedule in one calendar
3. **External for Extras**: Use external calendars for meet schedules, special events
4. **Color Coding**: Use Google Calendar colors to differentiate event types

### Access Control
1. **Admin Only**: Only admins should have access to Calendar Settings
2. **Verify Sharing**: Always check calendar sharing before adding external calendars
3. **Public vs Private**: Only make calendars public that all athletes should see
4. **Regular Audits**: Periodically review external calendar list and remove unused ones

### Troubleshooting

**Events Not Appearing:**
- Check that calendar is properly selected in settings
- Verify Google Calendar sharing permissions
- Ensure admin has granted Calendar API permissions
- Try removing and re-adding the calendar

**External Calendar Issues:**
- Use "Check Sharing" to verify permissions
- Confirm calendar email is exactly correct
- Make sure external calendar owner has shared with your admin account
- Check that external calendar is actually public or shared

**Performance Issues:**
- Limit external calendars to 3-5 maximum
- Large calendars (>500 events) may slow down sync
- Consider archiving old events in Google Calendar

## User Experience

### For Users (Athletes/Parents)
- View all public calendar events in the main calendar view
- Events from public and external calendars display seamlessly together
- Color-coded events if configured in Google Calendar
- Tap events to see full details (title, description, location, time)
- No configuration needed - just open the app and view events

### For Admins
- Full control over which calendars are visible
- Easy add/remove external calendars
- Verify calendar sharing status
- Create, edit, delete events on public calendar
- All calendar management in one centralized location

## Security & Privacy

- **Read-Only Access**: Users can only view events, not modify
- **OAuth Security**: Google authentication using industry-standard OAuth 2.0
- **Token Storage**: OAuth tokens encrypted and stored securely
- **Minimal Permissions**: App requests only necessary calendar permissions
- **Admin Control**: Only admins can change calendar configuration

## Common Use Cases

### Scenario 1: Weekly Practice Schedule
1. Admin creates "BPVA Practice" calendar in Google Calendar
2. Adds weekly recurring practice events (Mon/Wed/Fri 4-6pm)
3. Makes this calendar public in BPVA app
4. All athletes see practice schedule automatically

### Scenario 2: Adding Meet Schedule
1. Head coach shares their "Pole Vault Meets 2024" calendar
2. Admin adds coach's calendar email as external calendar
3. Verifies sharing is working
4. Athletes see both practices and meets in one view

### Scenario 3: Multiple Locations
1. Facility has indoor and outdoor calendars
2. Admin makes outdoor calendar public during summer
3. Adds indoor calendar as external during winter
4. Switches public calendar when season changes

## Future Enhancements

Potential features under consideration:
- Multiple public calendars simultaneously
- User-specific calendar subscriptions
- Calendar category filters (Practice, Meets, Special Events)
- Push notifications for upcoming events
- Calendar event RSVP/attendance tracking
- Calendar export to personal Google Calendar
