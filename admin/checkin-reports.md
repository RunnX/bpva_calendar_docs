---
layout: default
title: Check-In Reports
description: View detailed attendance and check-in analytics
---

# Gym Check-In Reports & Analytics

## Overview

The gym check-in reporting system provides detailed attendance analytics and historical tracking. The system archives older check-in data to maintain performance while keeping complete historical records accessible.

**Key Benefits:**
- Fast dashboard performance even with thousands of check-ins
- Complete historical data preserved
- Daily, weekly, and monthly statistics
- Individual athlete attendance tracking
- Lifetime check-in counts

## How the System Works

**Two Types of Data Storage:**

1. **Recent Check-Ins** (Last 30-90 days)
   - Detailed individual check-in records
   - Used for recent activity and athlete tracking
   - Faster queries for current statistics

2. **Archived Summaries** (Older than 90 days)
   - Daily summary records for historical periods
   - Preserves statistics without storing every individual check-in
   - Saves storage space while keeping historical data
   - Combined with recent data for complete totals

**Example:**
- **Recent**: Individual check-ins from last 60 days (detailed records)
- **Archived**: Daily totals from months/years ago (summary only)
- **Dashboard**: Combines both for accurate statistics

## Viewing Check-In Reports

### Accessing Reports

**From Admin Dashboard:**
1. Open Admin Dashboard (hamburger menu in top-left)
2. Statistics are visible on the main dashboard screen
3. For detailed reports, go to User Management → Manage Users
4. View check-in statistics section

**From User Management:**
1. Open Admin Dashboard (hamburger menu)
2. Tap **User Management** → **Manage Users**
3. Scroll to **"Gym Check-In Statistics"** section
4. View reports and archive options

### Statistics Available

**Dashboard Quick Stats:**
- **Today**: Number of check-ins so far today
- **This Week**: Check-ins from Monday through today
- **This Month**: Check-ins from 1st of month through today
- **Total**: All-time check-ins (recent + archived)
- **Unique Users**: Count of different athletes who have checked in

**Individual Athlete Stats:**
- Total lifetime check-ins for each athlete
- Last check-in date and time
- Check-in frequency (average per week/month)
- Attendance trends

**Date Range Reports:**
- Select custom date ranges
- View check-ins for specific periods
- Compare time periods
- Export data for analysis
- Real-time progress indicator
- Detailed results showing:
  - Check-ins processed
  - Aggregates created
  - Check-ins deleted (if applicable)
  - Date range archived

**Recommended Usage:**
- Archive check-ins older than 30-60 days
- Enable "Delete after archive" to save storage
- Run monthly or as needed when performance degrades

## Firestore Rules

```javascript
match /gym_checkin_aggregates/{dateId} {
  // All authenticated users can read aggregates
  allow read: if request.auth != null;
  // Only admins can create/update aggregates (via archive process)
  allow create, update: if isAdminUser();
  // Only admins can delete aggregates
  allow delete: if isAdminUser();
}
```

## Performance Benefits

### Before Aggregates
- Dashboard queries all check-ins (potentially thousands)
- Slow queries as data grows over time
- Higher Firestore read costs

### After Aggregates
- Dashboard queries recent check-ins + aggregates
- Fast queries regardless of historical data size
- Lower Firestore read costs
- Individual check-ins can be deleted after archiving

### Example Savings
With 2,000 check-ins over 6 months:
- **Before**: 2,000 document reads per stats query
- **After**: ~30 recent check-ins + 180 aggregates = 210 reads
- **Savings**: ~90% reduction in read operations

If deleting after archive:
- **Storage savings**: ~85% (only aggregates remain)

## Best Practices

### Archive Frequency

**Recommended schedule:**
- **Monthly**: Archive check-ins older than 30 days
- **Quarterly**: Archive check-ins older than 90 days
- **As needed**: If dashboard performance degrades

### Cutoff Date Selection

**Safe approach:**
1. Keep last 30 days as individual records (for detailed queries)
2. Archive everything older into aggregates
3. Delete old individual records to save storage

**Example workflow:**
```dart
// Archive everything before 30 days ago
final cutoffDate = DateTime.now().subtract(Duration(days: 30));
await LocationService.archiveGymCheckIns(
  cutoffDate: cutoffDate,
  deleteAfterArchive: true,  // Save storage
);
```

### When NOT to Delete

Keep individual records if you need:
- Detailed location data for old check-ins
- Distance calculations for historical records
- Ability to modify/delete specific old check-ins

In these cases:
```dart
await LocationService.archiveGymCheckIns(
  cutoffDate: cutoffDate,
  deleteAfterArchive: false,  // Keep originals
);
```

## Automated Archiving (Future Enhancement)

Consider adding a Cloud Function to run monthly:

```typescript
// fcm_listener_function/src/index.ts
exports.monthlyArchive = functions.pubsub
  .schedule('0 0 1 * *')  // First day of each month
  .timeZone('America/New_York')
  .onRun(async (context) => {
    const cutoffDate = new Date();
    cutoffDate.setDate(cutoffDate.getDate() - 30);
    
    // Archive logic here
    return null;
  });
```

## Troubleshooting

### Archive Fails Partway

**Symptoms**: Some aggregates created, but error occurs
**Cause**: Firestore batch size exceeded or network error
**Solution**: Archive process uses 500-operation batches and commits incrementally. Partial progress is saved. Re-run the archive to complete.

### Stats Don't Match Expected Values

**Symptoms**: Dashboard shows different numbers after archiving
**Cause**: Overlapping data (same day in both collections)
**Solution**: Archive process creates aggregates for complete days only. getGymCheckInStats() handles overlaps correctly by checking dates.

### Can't Delete Aggregates

**Symptoms**: Delete operation denied
**Cause**: Only admins can delete aggregates
**Solution**: Ensure user has admin role in Firestore

## Migration Path

For existing deployments with no aggregates:

1. **Deploy code** (this update)
2. **Test with small archive** (last 2-3 days, no delete)
3. **Verify stats** match before/after
4. **Full archive** (30+ days ago, with delete)
5. **Monitor performance** improvement
6. **Schedule monthly** archives going forward

## Testing

### Manual Test Checklist

- [ ] Archive with `deleteAfterArchive = false` - verify aggregates created
- [ ] Check stats dashboard - numbers should match before/after
- [ ] Archive with `deleteAfterArchive = true` - verify records deleted
- [ ] Stats still accurate after deletion
- [ ] Re-archive same period - should be idempotent (no duplicates)
- [ ] Archive with no old records - should handle gracefully

### Test Data Setup

```dart
// Create test check-ins across multiple dates
for (int i = 0; i < 60; i++) {
  await FirebaseFirestore.instance.collection('gym_checkins').add({
    'userId': 'test_user_$i',
    'userEmail': 'test$i@example.com',
    'userName': 'Test User $i',
    'timestamp': Timestamp.fromDate(
      DateTime.now().subtract(Duration(days: i)),
    ),
    'distanceFromGym': 10.0,
    'location': {'latitude': 0.0, 'longitude': 0.0},
  });
}
```

## Related Files

- `lib/models/gym_checkin_aggregate.dart` - Data model
- `lib/services/location_service.dart` - Archive and query logic
- `lib/screens/admin/user_management_screen.dart` - Admin UI
- `firestore.rules` - Security rules for aggregates collection
- `GYM_CHECKIN_README.md` - Original check-in system docs
