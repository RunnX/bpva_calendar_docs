# BPVA Calendar - Automated Daily Check-In Report

This feature provides automated daily email reports with PDF attachments summarizing gym check-ins for the previous day.

## Features

- **Configurable Recipients**: Admin can select multiple users to receive reports
- **PDF Reports**: Professional PDF format with statistics and detailed check-in list
- **Automated Delivery**: Reports sent automatically every day at 6 AM Eastern Time
- **User-Friendly Configuration**: Easy-to-use admin interface for setup

## Setup Instructions

### 1. Install Cloud Function Dependencies

Navigate to the Cloud Functions directory and install the new packages:

```bash
cd fcm_listener_function
npm install
```

This will install:
- `nodemailer` - for sending emails
- `pdfkit` - for generating PDF reports
- `@types/pdfkit` - TypeScript definitions

### 2. Configure Email Credentials

The Cloud Function needs SMTP credentials to send emails. Configure these using a `.env` file:

#### Step 1: Create the .env file

```bash
cd fcm_listener_function
cp .env.example .env
```

#### Step 2: Edit the .env file

Open `.env` and fill in your SMTP credentials:

**For Gmail (Recommended for Development):**

```env
SMTP_HOST=smtp.gmail.com
SMTP_PORT=587
SMTP_USER=your-email@gmail.com
SMTP_PASSWORD=your-app-password
```

**Note**: For Gmail, you need to create an App Password:
1. Go to your Google Account settings
2. Navigate to Security > 2-Step Verification > App passwords
3. Generate a new app password for "Mail"
4. Use this password (not your regular Gmail password)

**For Custom SMTP Server:**

```env
SMTP_HOST=smtp.yourdomain.com
SMTP_PORT=587
SMTP_USER=your-email@domain.com
SMTP_PASSWORD=your-password
```

#### Step 3: Verify the .env file

Ensure the `.env` file is NOT committed to git (it's in `.gitignore`):

```bash
git status  # .env should not appear in the list
```

### 3. Deploy Cloud Functions

Deploy the new functions to Firebase:

```bash
cd fcm_listener_function
npm run deploy
```

Or deploy just the new function:

```bash
firebase deploy --only functions:scheduledDailyCheckInReport,functions:sendDailyCheckInReport
```

### 4. Configure Report Settings in the App

1. Open the BPVA Calendar app as an admin user
2. Navigate to the menu → **Configuration** → **Automated Reporting**
3. Configure the following:
   - **Enable/Disable**: Toggle automated reporting on/off
   - **Report Types**: Select which reports to enable (currently only Daily Check-In)
   - **Schedule Time**: Set the time reports should be sent (default: 6:00 AM)
   - **Timezone**: Select your timezone (default: America/New_York)
   - **Recipients**: Add users who should receive the reports

4. Click **Save Configuration**

### 5. Firestore Security Rules

Add the following rule to your `firestore.rules` to allow the admin to configure reports:

```javascript
match /app_settings/automated_reporting {
  // Admins can read and write automated reporting configuration
  allow read, write: if request.auth != null && 
    exists(/databases/$(database)/documents/user_memberships/$(request.auth.uid)) &&
    get(/databases/$(database)/documents/user_memberships/$(request.auth.uid)).data.membershipType == 'admin';
}
```

## Report Content

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

### Manual Test (Without Waiting for Schedule)

You can manually trigger the report function for testing:

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
