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

1. **Access Calendar Settings**
   - Open the BPVA Calendar app as an admin
   - Navigate to Admin Dashboard
   - Tap **"Calendar Settings"** button

2. **Authenticate with Google**
   - On first access, you'll be prompted to sign in with Google
   - Grant the app permission to access Google Calendar
   - This is a one-time setup per device

3. **Select Public Calendar**
   - View the list of calendars from your Google account
   - Find the calendar you want to make public (e.g., "BPVA Practice Schedule")
   - Tap the **"Make Public"** button next to that calendar
   - Confirm the selection

4. **Verify Configuration**
   - The selected calendar will show a green "Public Calendar" badge
   - All app users can now view events from this calendar
   - Events sync in real-time

### Adding External Calendars

External calendars allow you to display events from calendars owned by other Google accounts (e.g., a coach's personal calendar with meet schedules).

1. **Get Calendar Email**
   - Have the external calendar owner share their calendar's email address
   - Format: `calendarid@group.calendar.google.com` or personal email
   - Calendar must be set to "Public" or shared with your admin Google account

2. **Add External Calendar**
   - In Calendar Settings, tap **"Add External Calendar"**
   - Enter the calendar email address
   - Tap **"Add"**
   - The calendar will appear in the external calendars list

3. **Verify Sharing**
   - Tap **"Check Sharing"** next to the external calendar
   - If properly shared, you'll see a success message
   - If not shared, you'll receive instructions to fix permissions

4. **Troubleshooting External Calendars**
   - If events don't appear, verify the calendar is actually shared
   - Check that the email address is correct
   - Ensure the external calendar owner has set proper sharing permissions
   - Remove and re-add the calendar if issues persist

### Changing the Public Calendar

1. Navigate to **Calendar Settings**
2. Find the new calendar you want to make public
3. Tap **"Make Public"** on the new calendar
4. The previous public calendar automatically becomes private
5. All users see events from the new public calendar immediately

### Removing External Calendars

1. In Calendar Settings, find the external calendar
2. Tap the **"Remove"** button
3. Confirm deletion
4. The calendar no longer appears for any users

## Calendar Data Flow

### Public Calendar
```
Admin Selects Calendar → Stored in Firestore (admin_settings) → 
All Users Query Firestore → App Fetches Events → Display in Calendar View
```

### External Calendars
```
Admin Adds Calendar Email → Stored in Firestore (admin_settings/external_calendars) →
All Users Query External Calendars → App Fetches Events → Merge with Public Events → Display
```

## Technical Details

### Firestore Structure

**Admin Settings** (`admin_settings/{adminEmail}`)
```json
{
  "selectedCalendarId": "primary",
  "selectedCalendarName": "BPVA Practice Schedule",
  "lastUpdated": "2024-01-15T10:30:00Z"
}
```

**External Calendars** (`admin_settings/{adminEmail}/external_calendars/{calendarId}`)
```json
{
  "calendarEmail": "meets@example.com",
  "calendarName": "Regional Meets",
  "addedAt": "2024-01-15T11:00:00Z",
  "isShared": true
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
