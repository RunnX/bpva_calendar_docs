# Release Notes - Version 2.0.3 (Build 71)

## App Store — What's New (iOS)

```
What's new in 2.0.3

Front-desk check-in
• Faster, more private check-in at the gym. The front-desk iPad now shows only what staff need to check you in (name, photo, membership status and a contact), not your full profile.
• Check-ins and session counts are now recorded securely in one step, so your remaining sessions always stay accurate.
• Linking a parent when signing up a walk-in athlete at the desk now works reliably.

Fixes and improvements
• Email fields on the registration form no longer auto-capitalize or autocorrect.
• Admins can now give a person extra roles (for example, an athlete who also coaches) from User Management → Edit Roles.
• General stability and performance improvements.
```

## Google Play — What's New (Android, 500 character limit)

```
• Faster, more private front-desk check-in: the gym iPad now shows only what staff need to check you in
• Vault Diagnosis no longer crashes on 4K videos
• Better full-screen display on newer Android phones
• Smaller, faster app
• Email fields on the registration form no longer auto-capitalize
• Admins: new role editor in User Management
• Bug fixes and stability improvements
```

---

## Full Details

### 🛎️ Front Desk, Rebuilt for Privacy
- **Roster instead of full records**: The shared front-desk iPad reads a minimal roster (name, photo, status, parent or emergency contact, last check-in) instead of members' full profiles and membership records
- **Server-side check-in**: Check-in, session validation and session deduction happen in one secure step on the server, so session counts can't drift or be edited at the desk
- **Parent lookup by exact email or phone**: The desk can only find a parent by their exact email or 10-digit phone number, and sees just their name
- **Walk-in parent linking fixed**: Linking a parent while adding a walk-in athlete no longer makes the whole save fail

### 👥 Roles (Admins)
- **Edit Roles**: User Management → a user → Edit Roles lets admins add extra roles (athlete, parent, BPVA coach, external coach, front desk, admin) on top of what the membership type grants

### 🐛 Bug Fixes & Improvements
- Fixed Vault Diagnosis being closed by Android when analyzing 4K video (Android)
- Email fields on the registration form no longer auto-capitalize or autocorrect (iOS)
- Edge-to-edge display on all supported Android versions
- Smaller, better-optimized Android app (R8)
- Ads stay off unless explicitly enabled

---

Requires the production Cloud Functions, Firestore/Storage rules and a `rebuildDeskRoster` run (all done 2026-11-13) before release.
