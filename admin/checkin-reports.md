# Gym Check-In Aggregates System

## Overview

The gym check-in aggregates system improves query performance by archiving old individual check-in records into daily summary records. This allows the dashboard to efficiently display statistics even with thousands of historical check-ins.

## Architecture

### Collections

1. **`gym_checkins`** - Individual check-in records (current/recent data)
2. **`gym_checkin_aggregates`** - Daily aggregate snapshots (archived data)

### Data Flow

```
New check-in → gym_checkins collection
              ↓
         (time passes)
              ↓
    Archive process triggered
              ↓
    Old check-ins grouped by date
              ↓
    Daily aggregates created in gym_checkin_aggregates
              ↓
    (Optional) Individual check-ins deleted
              ↓
    Dashboard queries both collections and combines stats
```

## Models

### GymCheckInAggregate

Located: `lib/models/gym_checkin_aggregate.dart`

```dart
class GymCheckInAggregate {
  final DateTime date;                    // Date this aggregate represents (YYYY-MM-DD)
  final DateTime snapshotCreatedAt;       // When this snapshot was created
  final int checkInCount;                 // Total check-ins for this date
  final int uniqueUserCount;              // Unique users who checked in
  final List<String> userIds;             // List of user IDs (Firebase UIDs)
  final List<String>? userEmails;         // Optional user emails for reference
}
```

**Document ID Format**: `YYYY-MM-DD` (e.g., `2025-12-24`)

This makes querying by date range simple and efficient.

## Services

### LocationService Methods

#### archiveGymCheckIns()

Archives old check-ins into daily aggregate records.

```dart
static Future<Map<String, dynamic>> archiveGymCheckIns({
  required DateTime cutoffDate,
  bool deleteAfterArchive = false,
}) async
```

**Parameters:**
- `cutoffDate`: Archive all check-ins before this date (exclusive)
- `deleteAfterArchive`: If true, deletes individual check-ins after aggregating (recommended to save storage)

**Returns:**
```dart
{
  'success': true/false,
  'message': 'Archive completed successfully',
  'checkInsProcessed': 150,
  'aggregatesCreated': 30,
  'checkInsDeleted': 150,  // Only if deleteAfterArchive = true
  'dateRange': {
    'oldest': '2025-01-01',
    'newest': '2025-11-24'
  }
}
```

**Process:**
1. Queries all check-ins before cutoff date
2. Groups check-ins by date
3. Creates aggregate record for each date with:
   - Total check-in count
   - Unique user count
   - List of user IDs
   - List of user emails (optional)
4. Optionally deletes original check-in records
5. Uses batched writes (500 operations per batch) for performance

#### getAggregates()

Retrieves aggregate records for a date range.

```dart
static Future<List<GymCheckInAggregate>> getAggregates({
  DateTime? startDate,
  DateTime? endDate,
}) async
```

**Example:**
```dart
// Get aggregates for the last 30 days
final aggregates = await LocationService.getAggregates(
  startDate: DateTime.now().subtract(Duration(days: 30)),
  endDate: DateTime.now(),
);
```

#### getGymCheckInStats() - Updated

Now combines current check-ins with archived aggregates.

**Logic:**
1. Get all current (non-archived) check-ins
2. Count today's check-ins from current data
3. Get aggregates for week/month date ranges
4. Combine current + aggregate counts for accurate totals
5. Aggregate unique users across all sources

**Returns:**
```dart
{
  'todayCheckIns': 15,      // Today only (from gym_checkins)
  'weekCheckIns': 85,       // Current + aggregates for this week
  'monthCheckIns': 350,     // Current + aggregates for this month
  'totalCheckIns': 2450,    // All current + all aggregates
  'uniqueUsers': 125,       // Unique across all data
}
```

## Admin UI

### Archive Dialog

Located in: `lib/screens/admin/user_management_screen.dart`

Accessible from: User Management screen → "Archive Old Check-Ins" button (next to Gym Check-In Statistics title)

**Features:**
- Date picker to select cutoff date
- Checkbox to delete individual records after archiving
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
