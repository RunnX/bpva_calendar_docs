# Indoor Meet Management

## Overview

The Indoor Meet Management system allows administrators to create, manage, and track indoor pole vault competitions. Athletes can register for meets, admins can monitor registrations, and coaches can manage meet details all within the BPVA Calendar app.

## Features

### Meet Creation & Management

**Create Indoor Meets**
- Set meet name and location
- Define date and time
- Set registration deadline
- Specify athlete eligibility requirements
- Add meet details and rules
- Set entry fees (if applicable)

**Edit Meet Details**
- Update meet information
- Change registration deadlines
- Modify location or time
- Add supplemental instructions
- Cancel or postpone meets

**Meet Status**
- **Upcoming**: Registration open
- **Registration Closed**: No new registrations
- **In Progress**: Currently happening
- **Completed**: Past meet with results
- **Cancelled**: Meet cancelled

### Athlete Registration

**Registration Process**
1. Athletes view upcoming meets
2. Review meet details and requirements
3. Tap "Register" button
4. Confirm registration
5. Receive confirmation notification

**Registration Requirements**
- Must be active member (non-expired)
- Must have approved waiver on file
- Must meet age/skill requirements (if specified)
- Registration before deadline

**Registration Status**
- **Registered**: Successfully registered
- **Waitlist**: Meet at capacity, on waiting list
- **Withdrawn**: Athlete cancelled registration
- **Not Registered**: Haven't registered yet

### Meet Roster Management

**View Registrations**
- Complete list of registered athletes
- Athlete names, emails, registration dates
- Emergency contact information
- Special accommodations or notes
- Export roster to PDF/CSV

**Manage Roster**
- Add athletes manually (walk-up registrations)
- Remove athlete registrations
- Move athletes from waitlist to registered
- Send notifications to all registrants

**Attendance Tracking**
- Mark athletes as checked in at meet
- Track no-shows
- Record participation status

### Meet Day Operations

**Check-In Athletes**
- Scan athlete QR codes or search by name
- Verify registration status
- Confirm emergency contacts
- Issue athlete numbers or bibs
- Pole checkout integration

**Pole Rentals for Meets**
- Athletes can checkout poles for meet
- Track which poles are at external locations
- Set expected return dates
- Bulk checkout for multiple athletes
- QR code scanning for quick assignment

**Competition Management**
- Record opening heights
- Track attempts and clearances
- Enter final results
- Publish results to athletes

## Data Model

### IndoorMeet
```dart
{
  "id": "meet_uuid",
  "name": "BPVA Winter Indoor Meet #1",
  "location": "BPVA Indoor Facility",
  "date": "2024-02-10T10:00:00Z",
  "registrationDeadline": "2024-02-08T23:59:59Z",
  "maxParticipants": 30,
  "entryFee": 25.00,
  "details": "Indoor pole vault meet for all skill levels...",
  "requirements": {
    "minAge": 8,
    "maxAge": null,
    "skillLevel": "all",
    "waiverRequired": true
  },
  "status": "upcoming",
  "createdBy": "admin_uid",
  "createdAt": "2024-01-15T10:00:00Z",
  "updatedAt": "2024-01-20T14:30:00Z"
}
```

### MeetRegistration
```dart
{
  "id": "registration_uuid",
  "meetId": "meet_uuid",
  "athleteId": "athlete_uid",
  "athleteName": "John Smith",
  "athleteEmail": "athlete@example.com",
  "registeredAt": "2024-01-25T08:15:00Z",
  "status": "registered",
  "emergencyContact": {
    "name": "Jane Smith",
    "phone": "555-0123",
    "relationship": "Mother"
  },
  "specialNotes": "First competition, may be nervous",
  "paidEntryFee": true,
  "checkedIn": false,
  "checkedInAt": null,
  "openingHeight": "7'0\"",
  "finalHeight": "9'6\"",
  "placing": 3
}
```

## Workflows

### Creating a New Meet

1. **Access Meet Management**
   - Navigate to Admin Dashboard
   - Tap "Indoor Meets"
   - Tap "Create New Meet" button

2. **Enter Meet Details**
   - Meet name (e.g., "Winter Series #1")
   - Location (defaults to BPVA facility)
   - Date and start time
   - Registration deadline
   - Maximum participants (optional)
   - Entry fee (if applicable)

3. **Set Requirements**
   - Age restrictions (if any)
   - Skill level requirements
   - Required documents (waiver always required)
   - Special equipment needs

4. **Add Details & Rules**
   - Meet description
   - Competition format
   - Opening height rules
   - Special instructions
   - Contact information

5. **Publish Meet**
   - Review all details
   - Tap "Create Meet"
   - Meet appears in upcoming meets list
   - Athletes can start registering
   - Notification sent to all athletes

### Managing Registrations

**Before Deadline**
1. Monitor registration numbers in real-time
2. View athlete list as they register
3. Send reminders to athletes who haven't registered
4. Answer questions via app messaging

**After Deadline**
1. Close registration (automatic at deadline)
2. Review final roster
3. Export roster for meet day
4. Send final meet details to all registrants
5. Prepare athlete numbers/bibs

**Waitlist Management**
1. If meet reaches capacity, new registrations go to waitlist
2. If athlete withdraws, move waitlist to registered
3. Send notification to waitlist athlete
4. Update roster

### Meet Day Operations

**Setup (Before Athletes Arrive)**
1. Print athlete roster
2. Prepare check-in station
3. Have emergency contact list available
4. Set up pole checkout area
5. Prepare athlete numbers/bibs

**Athlete Check-In**
1. Open meet check-in screen
2. Athletes arrive and check in:
   - Scan athlete QR code OR search by name
   - Verify registration status
   - Confirm emergency contact info is current
   - Issue athlete number
   - Direct to warmup area

**During Competition**
1. Track athletes as they compete
2. Record opening heights
3. Enter attempts (make/miss)
4. Update leaderboard in real-time
5. Handle equipment issues (pole checkouts)

**Post-Competition**
1. Record final results for all athletes
2. Mark meet as "Completed"
3. Publish results
4. Send thank you message to participants
5. Track pole returns from meet rentals

## Integration with Other Features

### Pole Inventory Integration

**Meet Pole Checkout**
- Athletes can checkout poles specifically for meets
- Track poles at external locations (if meet is away)
- Set expected return date (day after meet)
- Bulk checkout for multiple athletes going to same meet
- QR code scanning for quick assignment
- Automatic reminders for unreturned poles

**Meet Day Workflow**
1. Athlete registers for meet
2. Requests pole rental in app
3. Admin approves and assigns pole
4. Athlete picks up pole day before meet
5. Takes pole to competition
6. Returns pole after meet
7. System tracks entire lifecycle

### Calendar Integration

**Automatic Calendar Events**
- Creating a meet automatically adds to public calendar
- Meet date/time syncs with Google Calendar
- Athletes see meets in their calendar view
- Registration deadline shows as separate event
- Updates to meet details update calendar event

### Document Management Integration

**Waiver Verification**
- Registration checks for approved waiver
- Athletes without waivers cannot register
- Prompts athlete to upload waiver if missing
- Admin can override for special cases
- Emergency waivers at meet check-in (paper backup)

### Notification Integration

**Automated Notifications**
- Meet announcement (when created)
- Registration confirmation (when athlete registers)
- Registration deadline reminder (3 days before)
- Final meet details (1 day before)
- Results published notification
- Thank you message post-meet

## Best Practices

### Meet Planning

**Timing**
- Create meets 4-6 weeks in advance
- Set registration deadlines 2-3 days before meet
- Allow sufficient time for athlete preparation
- Consider school schedules and holidays

**Capacity Management**
- Set realistic participant limits based on facility
- Account for staff available to run meet
- Consider time constraints (2-3 athletes per hour for vault)
- Plan for waitlist if popular meet

**Communication**
- Send meet details as soon as created
- Reminder at 2 weeks, 1 week, 3 days, 1 day before
- Clear instructions for meet day (arrival time, what to bring)
- Post-meet results and thank you within 24 hours

### Registration Management

**Eligibility**
- Clearly state requirements upfront
- Enforce membership and waiver requirements
- Be consistent with age/skill restrictions
- Communicate reasons if someone can't register

**Waitlist Handling**
- First-come-first-served from waitlist
- Notify immediately when spot opens
- Give short deadline to confirm (24 hours)
- Move to next person if no response

**Withdrawals**
- Allow withdrawals up to registration deadline
- After deadline, case-by-case basis
- Track withdrawal reasons for future planning
- Move waitlist athletes when spots open

### Meet Day Execution

**Check-In Process**
- Start check-in 45-60 minutes before competition
- Verify emergency contacts at check-in
- Have paper backup roster in case of tech issues
- Assign athlete numbers in check-in order

**Safety First**
- Confirm all waivers are on file
- Have emergency contacts readily accessible
- Brief athletes on facility rules
- Coach supervision at all times

**Results Management**
- Record all attempts accurately
- Double-check final placements
- Publish results same day if possible
- Celebrate achievements (PRs, first meet, etc.)

## Reporting & Analytics

### Meet Statistics
- Total meets per season
- Average participants per meet
- Registration trends over time
- Most popular meet times/dates
- Waitlist frequency

### Athlete Participation
- Athletes by number of meets attended
- Progression tracking (opening to final heights)
- PR achievements at meets
- Attendance rates after registration

### Financial Tracking (if entry fees)
- Revenue per meet
- Payment collection rates
- Refunds issued
- Net revenue after expenses

## Troubleshooting

**Athlete Can't Register**
- Check membership expiration date
- Verify waiver is approved
- Confirm meets age/skill requirements
- Check if meet is at capacity

**Registration Not Showing**
- Refresh athlete's app
- Check Firestore sync
- Verify registration was completed
- Look for error logs

**Check-In Issues**
- Have paper backup roster
- Use manual search instead of QR scan
- Verify athlete is on registered list
- Check for duplicate names

## Future Enhancements

Potential features under consideration:
- Online payment for entry fees
- Real-time scoring leaderboard
- Live streaming integration
- Athlete seeding by skill level
- Team scoring for clubs
- Multi-day meet support
- Travel meet management
- Meet series championships
- Parent spectator registration
- Results export to USATF/AAU formats
