# Pole Checkout for Indoor Meets

## Overview

The Pole Checkout system for indoor meets allows athletes to rent poles specifically for competitions. This feature integrates with the pole inventory management system and indoor meet registration to streamline the process of getting athletes the right equipment for meets.

## Features

### Meet-Specific Pole Checkout

**Purpose**
- Allow athletes to checkout poles for specific meets
- Track poles at external locations (away meets)
- Ensure poles are returned after competitions
- Prevent inventory loss
- Coordinate equipment needs for multiple athletes

### Checkout Workflow

**Before the Meet**
1. Athlete registers for indoor meet
2. Athlete requests pole rental in app or contacts coach
3. Admin/coach assigns appropriate pole(s)
4. System tracks pole checkout with meet information
5. Athlete picks up pole before meet
6. Pole marked as "Checked Out - Meet"

**At the Meet**
1. Athlete competes with checked-out pole
2. Optional: Track pole usage in competition results
3. Coach monitors pole condition

**After the Meet**
1. Athlete returns pole
2. Admin scans pole barcode or manually checks in
3. Pole status returns to "Available"
4. System logs complete rental history

### Bulk Checkout for Meets

**Team Checkout**
When multiple athletes need poles for the same meet:
1. Admin opens meet details
2. Selects "Bulk Pole Checkout"
3. Views list of registered athletes
4. Assigns poles to multiple athletes at once
5. Prints checkout manifest
6. Athletes pick up poles together

**Benefits**
- Save time vs individual checkouts
- Coordinate transportation
- Track all meet poles in one place
- Ensure proper inventory before travel

### QR Code Scanning

**Quick Checkout**
1. Admin opens pole checkout screen
2. Taps "Scan Pole" button
3. Scans pole's QR code with camera
4. System automatically:
   - Identifies the pole
   - Checks current status
   - Checks out if available OR checks in if already out
   - Prompts for athlete assignment (if checking out)
   - Shows success confirmation

**Instant Check-In**
1. Athlete returns with pole
2. Admin scans QR code
3. System detects pole is checked out
4. Automatically checks in pole
5. Updates status to Available
6. Records return date and condition notes

### Meet Pole Management Dashboard

**View All Meet Poles**
- See all poles currently checked out for meets
- Filter by specific meet
- View expected return dates
- See which athletes have which poles
- Identify overdue returns

**Tracking & Reminders**
- Automatic reminders day after meet
- Email notifications for unreturned poles
- Visual warnings for late returns
- Contact info for athletes with overdue poles

## Integration with Other Features

### Pole Inventory

**Pole Status Updates**
- Checking out pole changes status from "Available" to "Checked Out"
- Returning pole changes status back to "Available"
- Poles can be marked "Maintenance" if damaged at meet
- Complete history tracked in pole record

**Barcode System**
- Each pole has unique barcode/QR code
- QR codes printed on pole labels
- Scanning prevents manual entry errors
- Works even if pole is dirty or label partially damaged (high error correction)

### Indoor Meet System

**Registration Integration**
- Pole request added to meet registration
- Admins see pole needs when viewing meet roster
- Athletes can specify preferred pole specs
- System suggests appropriate poles based on athlete's history

**Meet Day Checklist**
- Pre-meet: Verify all poles checked out and picked up
- During meet: Track pole usage
- Post-meet: Check all poles back in
- Maintenance: Inspect poles for damage

### Athlete Profiles

**Athlete Pole History**
- View athlete's previous pole checkouts
- See which poles they've used successfully
- Identify preferred pole characteristics
- Make better assignment decisions

**Automated Suggestions**
When athlete requests pole for meet:
- System shows poles they've used before
- Highlights poles matching their runway step config
- Suggests appropriate weight/length based on PR height
- Shows availability of preferred poles

## Data Model

### Pole Checkout Record
```dart
{
  "id": "checkout_uuid",
  "poleId": "pole_uuid",
  "athleteId": "athlete_uid",
  "athleteName": "John Smith",
  "athleteEmail": "athlete@example.com",
  "checkoutDate": "2024-02-08T16:00:00Z",
  "expectedReturnDate": "2024-02-11T18:00:00Z",
  "actualReturnDate": null,
  "status": "checked_out",
  "purpose": "meet",
  "meetId": "meet_uuid",
  "meetName": "BPVA Winter Indoor #1",
  "meetLocation": "BPVA Facility",
  "meetDate": "2024-02-10T10:00:00Z",
  "notes": "Athlete's first meet, using familiar pole",
  "checkoutMethod": "qr_scan",
  "checkedOutBy": "admin_uid",
  "returnCondition": null,
  "maintenanceRequired": false
}
```

## Workflows

### Individual Pole Checkout for Meet

1. **Athlete Requests Pole**
   - Registers for meet
   - Adds pole request in notes OR messages coach
   - Specifies preferred pole characteristics

2. **Admin Reviews Request**
   - Opens meet roster
   - Views athlete's pole request
   - Checks athlete's previous pole usage
   - Reviews runway step configuration

3. **Admin Assigns Pole**
   - Navigates to Pole Inventory
   - Filters for available poles matching specs
   - Selects appropriate pole
   - Taps "Checkout to Athlete"
   - Links to specific meet
   - Sets expected return date (day after meet)
   - Adds any notes

4. **Pole Pickup**
   - Athlete arrives to pick up pole
   - Admin verifies checkout record
   - Scans pole QR code to confirm
   - Hands pole to athlete
   - Reminds about return deadline

5. **Pole Return**
   - Athlete brings pole back after meet
   - Admin scans QR code
   - System auto-detects checkout record
   - Prompts for condition assessment
   - Admin inspects pole
   - Confirms return
   - Pole status updates to Available

### Bulk Checkout for Team Meet

1. **Pre-Meet Planning**
   - Admin opens indoor meet details
   - Views complete roster of athletes
   - Reviews pole requests from athletes
   - Checks inventory availability

2. **Assign Poles to Multiple Athletes**
   - Taps "Bulk Pole Checkout"
   - See list of registered athletes
   - For each athlete:
     - Search/filter available poles
     - Assign pole
     - Set same expected return date for all
   - Review all assignments
   - Confirm bulk checkout

3. **Generate Checkout Manifest**
   - System creates printable list
   - Shows: Athlete name, assigned pole, specs
   - Print manifest for reference
   - Use at pole pickup time

4. **Coordinated Pickup**
   - Team gathers at scheduled time
   - Athletes check in from manifest
   - Scan each pole as handed out
   - Double-check assignments
   - Athletes sign manifest (optional)

5. **Post-Meet Bulk Check-In**
   - Athletes return poles together
   - Admin scans each pole QR code
   - System matches to checkout records
   - Marks all as returned
   - Inspects each pole for damage
   - Updates all statuses to Available

### Managing Overdue Poles

1. **Automatic Detection**
   - System checks daily for overdue poles
   - Expected return date has passed
   - Pole still shows "Checked Out" status

2. **Admin Notification**
   - Dashboard shows overdue poles
   - Red warning indicator
   - Lists athlete name and contact info
   - Shows how many days overdue

3. **Contact Athlete**
   - Send in-app message reminder
   - Email notification if enabled
   - Phone call if necessary
   - Extend deadline if athlete has valid reason

4. **Escalation**
   - If pole not returned after 7 days, mark as "Lost"
   - Contact parent/guardian
   - Assess replacement fee
   - File incident report

## Best Practices

### Pole Assignment

**Match to Athlete**
- Consider athlete's weight and height
- Review runway step configuration
- Check previous successful poles
- Factor in meet competition level

**Pole Condition**
- Only checkout poles in good condition
- Inspect before assigning
- Never send damaged pole to meet
- Keep best poles for important competitions

**Documentation**
- Photo of pole before checkout (optional)
- Note any existing cosmetic damage
- Document athlete acknowledgment
- Keep checkout manifest on file

### Meet Day Operations

**Pre-Meet Checklist**
- [ ] All poles checked out and picked up
- [ ] Backup poles identified if needed
- [ ] Pole transport arranged (if away meet)
- [ ] Pole return plan communicated

**At Meet**
- Keep track of which athletes have which poles
- Watch for pole damage during competition
- Have backup poles available if possible
- Document any issues

**Post-Meet**
- Inspect all poles as they're returned
- Address any damage immediately
- Update maintenance log
- Thank athletes for timely returns

### Inventory Protection

**Loss Prevention**
- Require checkout acknowledgment
- Set realistic return deadlines
- Follow up quickly on overdue poles
- Implement late fees if necessary (policy decision)

**Damage Management**
- Inspect poles thoroughly on return
- Mark damaged poles for maintenance immediately
- Don't re-checkout damaged poles
- Track damage patterns to improve handling education

## Reporting & Analytics

### Meet Pole Statistics
- Total poles checked out per meet
- Average checkout duration
- Return rate (on-time vs late)
- Damage rate at meets vs practices
- Most popular poles for meets

### Athlete Analytics
- Athletes by number of pole checkouts
- Average return time per athlete
- Damage incidents per athlete
- Preferred pole characteristics

### Inventory Insights
- Poles most frequently used at meets
- Poles never checked out (may not fit athlete base)
- Poles frequently damaged at meets (training issue?)
- Pole availability gaps (what sizes needed?)

## Troubleshooting

**Pole Shows as Available But Can't Find It**
- Check if actually returned (scan history)
- Look in maintenance area
- Ask most recent user
- Review checkout log for clues

**QR Code Won't Scan**
- Clean pole/label
- Use manual barcode entry
- Better lighting
- Replace damaged label

**Wrong Pole Assigned**
- Contact athlete immediately
- Arrange pole swap if before meet
- Document for future reference
- Update checkout record

**Pole Damaged at Meet**
- Document damage with photos
- Mark pole for maintenance
- Don't allow further use
- Assess if athlete or normal wear
- Update pole record

## Future Enhancements

Potential features under consideration:
- Athlete self-service pole checkout (with approval)
- Pole reservation system for meets
- Automated pole suggestion based on AI/ML
- Integration with meet results (which pole yielded PR)
- Pole GPS tracking for away meets
- Digital pole checkout contracts
- Photo requirements on return
- Damage fee automation
