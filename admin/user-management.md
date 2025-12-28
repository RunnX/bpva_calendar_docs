# User Management

## Overview

The User Management system provides comprehensive tools for administrators to manage all app users, assign roles, configure memberships, and link family relationships. This centralized system ensures proper access control and data organization across the BPVA Calendar app.

## User Roles

### Role Hierarchy

1. **Admin**
   - Full system access
   - Manage all users and roles
   - Configure calendar settings
   - Access all admin features
   - Typically: Head coaches, facility managers

2. **Receptionist**
   - Front desk operations
   - Quick member check-in
   - View member list
   - Limited admin access
   - Typically: Front desk staff

3. **Parent**
   - View linked athlete information
   - Monitor athlete's check-ins and attendance
   - View athlete's document status
   - Manage family emergency contacts
   - Typically: Parents/guardians

4. **Athlete**
   - Full app functionality for pole vaulters
   - Gym check-in capability
   - View practice/meet calendars
   - Upload registration documents
   - Manage runway step configurations
   - Track personal progress
   - Typically: Pole vault athletes

5. **User** (Default)
   - Basic read-only access
   - View public calendars
   - No check-in or profile features
   - Typically: Visitors, prospective members

## Features

### User List & Search

**View All Users**
- Real-time list of all registered users
- Display name, email, role
- Search and filter capabilities
- Membership status indicators
- Quick role assignment

**Search Functionality**
- Search by name (first or last)
- Search by email address
- Filter by role type
- Sort by registration date
- View membership expiration

### Role Management

**Assign Roles**
1. Navigate to **Admin Dashboard → User Management**
2. Find the user in the list
3. Tap on the user to view details
4. Select new role from dropdown
5. Confirm role change
6. User's permissions update immediately

**Role Restrictions**
- Cannot remove the last admin (system protection)
- Cannot change your own role to prevent lockout
- Role changes are logged in admin actions
- Users notified of role changes via push notification

**Bulk Role Operations**
- Select multiple users
- Assign role to all selected
- Useful for new athlete groups
- Transaction-based for consistency

### Membership Management

**Membership Expiration**
- Set expiration date for each user
- Automatic expiration notifications
- Grace period configuration (default: 7 days)
- Expired users marked with warning indicator
- Reactivation workflow

**Membership Status**
- Active: Current paid member
- Expiring Soon: Within 30 days
- Expired: Past expiration date
- Inactive: Account disabled

**Setting Expiration Dates**
1. Open user details
2. Tap **"Set Membership Expiration"**
3. Select date from calendar picker
4. Optionally add notes
5. Save changes

### Family Linking (Parent-Athlete)

**Link Athletes to Parents**
1. Open parent's user profile
2. Tap **"Link Athlete"**
3. Search for athlete by name/email
4. Select athlete to link
5. Confirm relationship
6. Athlete appears in parent's linked athletes list

**Link Parents to Athletes**
1. Open athlete's user profile
2. Tap **"Link Parent"**
3. Search for parent by name/email
4. Select parent to link
5. Confirm relationship
6. Parent appears in athlete's linked parents list

**Benefits of Linking**
- Parents can view athlete's check-in history
- Parents see athlete's uploaded documents
- Shared emergency contact information
- Coordinated communication
- Family billing management (future)

**Multiple Relationships**
- Athletes can have multiple linked parents (mom, dad, guardian)
- Parents can have multiple linked athletes (siblings)
- Links are bidirectional (shows on both profiles)
- Easy unlink functionality

### Emergency Contacts

**Manage Emergency Contacts**
- Primary contact (required)
- Secondary contact (optional)
- Contact information: Name, phone, relationship
- Medical information fields
- Allergies and special instructions

**Access Levels**
- Athletes: Edit their own emergency contacts
- Parents: Edit linked athlete's emergency contacts
- Admins: View/edit all emergency contacts
- Emergency access in check-in system

### Admin List Management

**View Admins**
- Dedicated admin list screen
- Shows all users with admin role
- Email addresses and names
- Last login dates

**Add Admins**
1. Navigate to **Admin Dashboard → Admin List**
2. Tap **"Add Admin"**
3. Enter email address of user
4. User must already be registered in app
5. Confirm addition
6. User role automatically updated to Admin

**Remove Admins**
1. View admin list
2. Select admin to remove
3. Tap **"Remove Admin"**
4. Confirm removal (cannot remove last admin)
5. User role reverts to User

**Admin Safeguards**
- Cannot remove yourself
- Cannot remove the last admin (prevents lockout)
- All admin changes logged
- Audit trail for compliance

## Data Model

### UserProfile
```dart
{
  "uid": "firebase_auth_uid",
  "email": "athlete@example.com",
  "displayName": "John Smith",
  "photoURL": "https://...",
  "role": "athlete",
  "createdAt": "2024-01-15T10:00:00Z",
  "lastLogin": "2024-01-20T08:30:00Z"
}
```

### UserMembership
```dart
{
  "userId": "firebase_auth_uid",
  "membershipExpiration": "2024-12-31T23:59:59Z",
  "linkedParentIds": ["parent_uid_1", "parent_uid_2"],
  "linkedAthleteIds": ["athlete_uid_1", "athlete_uid_2"],
  "emergencyContact": {
    "primaryName": "Jane Smith",
    "primaryPhone": "555-0123",
    "primaryRelationship": "Mother",
    "secondaryName": "Bob Smith",
    "secondaryPhone": "555-0124",
    "secondaryRelationship": "Father"
  },
  "medicalInfo": {
    "allergies": "Peanuts",
    "medications": "Inhaler as needed",
    "specialInstructions": "Asthma - has rescue inhaler"
  }
}
```

## Workflows

### Onboarding New Athlete

1. **User Signs Up** (via app)
   - Creates account with Google/Apple Sign-In
   - Default role: User
   - Profile created automatically

2. **Admin Configures Account**
   - Navigate to User Management
   - Search for new user by email
   - Assign role: Athlete
   - Set membership expiration date
   - Link to parent account (if applicable)

3. **Athlete Completes Profile**
   - Upload registration form
   - Upload waiver documents
   - Add emergency contacts
   - Configure runway step settings

4. **Admin Reviews Documents**
   - Review submitted documents
   - Approve or request changes
   - Account fully activated

### Onboarding New Parent

1. **Parent Signs Up**
   - Creates account via app
   - Default role: User

2. **Admin Links to Athlete**
   - Open parent's profile
   - Link to athlete account(s)
   - Assign role: Parent

3. **Parent Access Granted**
   - Can view linked athlete info
   - See athlete check-ins
   - View athlete documents

### Adding New Admin

1. **User Must Exist**
   - User must have signed in at least once
   - Have valid email on file

2. **Admin Adds to List**
   - Go to Admin List
   - Add email address
   - Confirm addition

3. **Role Updated**
   - User role changes to Admin
   - Full admin access granted immediately
   - Notification sent to new admin

## Firestore Security

All user management operations enforce strict security rules:

```javascript
// User can only read their own profile
match /users/{userId} {
  allow read: if request.auth.uid == userId;
}

// Only admins can write user data
match /users/{userId} {
  allow write: if get(/databases/$(database)/documents/users/$(request.auth.uid)).data.role == 'admin';
}

// Athletes can read linked parents' memberships
match /user_memberships/{userId} {
  allow read: if request.auth != null && 
    exists(/databases/$(database)/documents/user_memberships/$(request.auth.uid)) &&
    userId in get(/databases/$(database)/documents/user_memberships/$(request.auth.uid)).data.linkedParentIds;
}
```

## Best Practices

### Role Assignment
- Assign roles promptly after new user registration
- Review role assignments quarterly
- Remove admin access when staff leaves
- Use least-privilege principle (start with User role)

### Membership Management
- Set expiration dates during signup
- Send renewal reminders 30 days before expiration
- Maintain grace period for late payments
- Archive expired members after 90 days

### Family Linking
- Verify relationships before linking
- Keep links updated (divorce, custody changes)
- Unlink when athlete becomes adult (18+)
- Respect privacy preferences

### Data Privacy
- Only collect necessary information
- Follow FERPA guidelines for youth data
- Secure emergency contact information
- Allow users to update their own data

## Common Tasks

### View User's Complete Information
1. Search for user in User Management
2. Tap user to open details
3. View tabs: Profile, Membership, Links, Documents, Check-ins

### Bulk Update Memberships
1. Export user list to CSV
2. Update expiration dates in spreadsheet
3. Import updated CSV (future feature)
4. Verify changes

### Handle Expired Membership
1. User receives notification at 30, 14, 7 days before expiration
2. Admin sees "Expiring Soon" badge
3. At expiration, check-in access disabled
4. Admin can extend membership or deactivate account

### Audit User Activity
1. View admin actions log
2. Filter by user or date range
3. Review role changes and membership updates
4. Export audit log for compliance

## Troubleshooting

**User Can't See Expected Features**
- Verify correct role is assigned
- Check membership expiration date
- Ensure account is active
- Try logging out and back in

**Parent Can't See Athlete Data**
- Verify link exists in both profiles
- Check that athlete hasn't unlinked
- Ensure parent has Parent role
- Refresh app data

**Can't Add New Admin**
- Verify user has signed in at least once
- Check that email exactly matches user's account
- Ensure you are an admin
- Look for typos in email address

**Membership Expiration Not Working**
- Check expiration date format
- Verify timezone settings
- Ensure Firestore rules allow updates
- Test with future date

## Integration Points

### With Other Features

**Check-In System**
- Membership status affects check-in eligibility
- Expired members cannot check in
- Role determines check-in features (athlete vs receptionist)

**Document Management**
- Athletes upload documents to their profile
- Parents view linked athlete documents
- Admins review all pending documents

**Calendar Access**
- All roles can view public calendars
- Admins can create/edit events
- Role determines calendar features

**Notifications**
- Role-based notification targeting
- Membership notifications automated
- Admin action notifications

## Reporting & Analytics

**User Statistics**
- Total registered users
- Users by role breakdown
- Active vs inactive members
- Membership expiration timeline
- New users per month

**Access Reports**
- Last login dates
- Feature usage by role
- Check-in frequency
- Document completion rates

## Future Enhancements

Potential improvements under consideration:
- Bulk import from CSV
- Advanced search filters
- Custom role creation
- User groups/teams
- Automated membership billing
- SMS notifications for expiration
- Self-service membership renewal
- User activity dashboards
