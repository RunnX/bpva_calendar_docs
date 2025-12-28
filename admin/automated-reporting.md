---
layout: default
title: Automated Reporting
description: Configure automated daily check-in reports via email
---

# Automated Daily Check-In Reports

## Overview

The Automated Daily Check-In Report feature sends professional email reports with PDF attachments summarizing gym check-ins from the previous day. Reports are automatically delivered every morning at 6 AM Eastern Time to selected admin and coach users.

**What You'll Get:**
- Daily email with PDF attachment
- Check-in statistics (total check-ins, unique users, average distance)
- Detailed check-in list with timestamps and athlete names
- Professional formatting with BPVA branding

**Who Can Receive Reports:**
- Administrators
- Coaches
- Receptionists
- Any combination of staff members you select

## Setting Up Automated Reports

### Step 1: Access Report Configuration

1. Open BPVA Calendar app as an admin
2. Navigate to the menu → **Admin Dashboard**
3. Tap **"Automated Reporting"** or **"Configuration"** → **"Automated Reporting"**

### Step 2: Configure Report Settings

**Enable Automated Reporting:**
- Toggle **"Enable Automated Reports"** to ON
- The system is now active and will send reports daily

**Select Report Types:**
- Currently supports: **Daily Check-In Report**
- Check the box to enable this report type
- Future: Additional report types may be added

**Set Schedule:**
- **Time**: Choose when reports should be sent (default: 6:00 AM)
- **Timezone**: Select your facility's timezone (default: America/New_York - Eastern)
- Reports cover check-ins from the previous day

**Add Recipients:**
1. Tap **"Add Recipient"**
2. Search for user by name or email
3. Select user from list
4. User appears in recipients list
5. Repeat for each person who should receive reports
6. You can add multiple recipients

**Remove Recipients:**
- Tap the X next to any recipient's name to remove them
- Changes take effect immediately

### Step 3: Save Configuration

1. Review all settings
2. Tap **"Save Configuration"**
3. Confirmation message appears
4. First report will be sent at the scheduled time

### Step 4: Verify Reports Are Working

**Check Email:**
- Wait for scheduled report time
- Check email inbox (and spam folder)
- Look for email from BPVA Calendar system
- Open PDF attachment to view report

**If Reports Don't Arrive:**
- Verify you're in the recipients list
- Check spam/junk folder
- Ensure email address in your profile is correct
- Contact system administrator

## Understanding the Report

The daily check-in report includes:

### Summary Statistics
- Total check-ins for the day
- Number of unique users
- Average distance from gym location

### Detailed Check-In List
For each check-in:
- Time of check-in
- User name
- User email
- Distance from gym (in meters)

### PDF Format
- Professional layout with BPVA branding
- Tabular format for easy reading
- Timestamp of report generation
- Organized by check-in time

## Schedule

The report function runs automatically:
- **Time**: 6:00 AM Eastern Time (configurable in the app UI)
- **Frequency**: Daily
- **Report Date**: Previous day's check-ins
- **Cloud Function**: `scheduledDailyCheckInReport`

## Testing

## Managing Reports

**Changing Schedule:**
1. Go back to Automated Reporting configuration
2. Adjust time or timezone
3. Save configuration
4. New schedule takes effect immediately

**Adding/Removing Recipients:**
- Changes take effect for the next scheduled report
- No need to restart or redeploy anything
- Recipients receive reports until removed from list

**Temporarily Disabling Reports:**
1. Toggle **"Enable Automated Reports"** to OFF
2. Save configuration
3. No reports will be sent
4. Turn back ON when ready to resume

## Troubleshooting

**Reports Not Being Received:**
- Check spam/junk email folder
- Verify recipient email address is correct in user profile
- Ensure you're in the recipients list
- Check that automated reporting is enabled
- Verify scheduled time hasn't passed today
- Contact system administrator if issue persists

**PDF Won't Open:**
- Try different PDF reader (Adobe Acrobat, Preview, browser)
- Download attachment rather than opening in email
- Check file isn't corrupted (re-send if needed)
- Ensure device has PDF reader installed

**Empty or Missing Data:**
- Report covers PREVIOUS day's check-ins, not current day
- If no check-ins yesterday, report will show zero
- Check that gym check-in feature is working correctly
- Verify athletes are actually checking in

**Wrong Timezone:**
- Update timezone in report configuration
- Report times will adjust to new timezone
- Check-in timestamps in report match gym's timezone

## Best Practices

**Recipients:**
- Include all coaches who need daily attendance info
- Add facility manager for oversight
- Include receptionist if they handle attendance
- Limit to staff who actually need the data

**Schedule:**
- 6 AM is good for morning review before practice
- Adjust if you have early morning sessions
- Consider staff work schedules
- Keep timezone updated if facility moves

**Using Report Data:**
- Review daily to spot attendance trends
- Follow up with athletes who haven't checked in
- Use stats for membership engagement
- Archive reports for historical records
- Compare week-to-week for growth tracking

## Privacy Considerations

**What's Included:**
- Check-in times and dates
- Athlete names and emails
- Distance from gym (to verify location)
- Aggregate statistics

**What's NOT Included:**
- Exact athlete GPS locations
- Personal medical information
- Payment information
- Private athlete notes

**Data Security:**
- Reports sent via secure email
- PDF files not publicly accessible
- Only designated recipients receive reports
- Athlete data handled per privacy policy

```bash
# Using Firebase CLI
firebase functions:shell

# Then in the shell:
sendDailyCheckInReport()
```

Or call it from the Firebase Console:
1. Go to Firebase Console → Functions
2. Find `sendDailyCheckInReport`
3. Click "Test function"

### Check Logs

View Cloud Function logs:

```bash
firebase functions:log --only scheduledDailyCheckInReport,sendDailyCheckInReport
```

## Troubleshooting

### No Emails Being Sent

1. **Check Configuration**: Verify SMTP credentials in `.env` file
   ```bash
   cd fcm_listener_function
   cat .env  # Verify credentials are set correctly
   ```

2. **Check if .env is deployed**: The `.env` file must be present when deploying
   - Ensure `.env` exists in `fcm_listener_function/` directory
   - Redeploy if you just created/updated the `.env` file

3. **Check if Enabled**: Ensure automated reporting is enabled in the app

4. **Check Recipients**: Verify recipients are configured

5. **Check Logs**: Look for errors in Cloud Function logs
   ```bash
   firebase functions:log --only scheduledDailyCheckInReport
   ```

### Gmail "Less Secure App" Error

If using Gmail and seeing authentication errors:
- Make sure you're using an App Password (not your regular password)
- Enable 2-Step Verification first
- Generate a new App Password specifically for this function

### No Check-Ins in Report

- The report only includes check-ins from the previous day
- Verify check-ins exist in the `gym_checkins` Firestore collection
- Check that the date range is correct

### PDF Generation Fails

Check Cloud Function logs for specific PDF generation errors. Common issues:
- Insufficient memory (may need to upgrade Cloud Function tier)
- Large number of check-ins (may need pagination)

## Architecture

### Flutter App Components

- **`automated_reporting_screen.dart`**: Admin UI for configuring reports
- **`report_configuration_service.dart`**: Service for managing Firestore configuration
- **Firestore Collection**: `app_settings/automated_reporting`

### Cloud Function Components

- **`daily_checkin_report.ts`**: Core report generation logic
- **`scheduledDailyCheckInReport`**: Scheduled function that runs daily
- **`sendDailyCheckInReport`**: Callable function for manual triggers

### Data Flow

1. Admin configures settings in Flutter app
2. Settings saved to Firestore `app_settings/automated_reporting`
3. Cloud Function runs on schedule (6 AM daily)
4. Function reads configuration from Firestore
5. Queries `gym_checkins` collection for previous day
6. Generates PDF report with statistics
7. Sends email with PDF attachment to configured recipients
8. Updates `lastSent` timestamp in Firestore

## Cost Considerations

### Cloud Functions
- Scheduled invocation: ~30 times/month (daily)
- Execution time: ~5-10 seconds per report
- Memory: 256 MB (default) should be sufficient for most use cases

### Email Sending
- Free tier: Most SMTP providers offer free tiers for low volume
- Gmail: Free for reasonable volume (hundreds per day)
- SendGrid: Free tier includes 100 emails/day

### Storage
- PDF generation is in-memory (not stored in Cloud Storage)
- No additional storage costs

## Future Enhancements

Potential improvements for future versions:

1. **Multiple Report Types**: Weekly summaries, monthly analytics
2. **Custom Report Scheduling**: Allow different schedules for different reports
3. **Report Customization**: Let admins choose which metrics to include
4. **Historical Reports**: Archive past reports in Cloud Storage
5. **Dashboard Analytics**: Visual analytics in the app
6. **Export Options**: CSV, Excel formats in addition to PDF
7. **Conditional Reporting**: Only send if there are check-ins
8. **Report Preview**: Preview report before scheduling

## Support

For issues or questions:
1. Check Cloud Function logs first
2. Verify all configuration steps are complete
3. Test with manual trigger before relying on schedule
4. Review Firestore security rules for access issues
