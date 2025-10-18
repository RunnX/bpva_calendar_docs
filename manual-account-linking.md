# Manual Account Linking System

## Overview

Since Firebase's native `linkWithCredential()` doesn't work reliably with Apple Sign-In, this app implements a **manual account linking system** that:

1. ✅ Complies with App Store requirements (offers both Google and Apple Sign-In)
2. ✅ Allows users to sign in with either Google or Apple
3. ✅ Maintains Google Calendar API access regardless of sign-in method
4. ✅ Supports different emails for Google and Apple accounts
5. ✅ Stores OAuth tokens securely in Firestore

## Architecture

### Key Concepts

**Primary Account**: The first account a user creates (Google or Apple)
- Has a unique Firebase UID
- All user data is stored under this UID
- Never changes once created

**Linked Account**: Additional sign-in method added later
- Doesn't create a new Firebase user
- Points to the primary account
- Emails don't need to match

**Google OAuth Tokens**: Stored with the primary account
- Required for Google Calendar API access
- Persists even when signed in with Apple
- Refreshed automatically

## How It Works

### Scenario 1: User Creates Account with Google, Links Apple Later

```
1. User signs in with Google
   └→ Firebase creates user with UID: abc123
   └→ Google OAuth tokens stored
   └→ Primary account established

2. User clicks "Link Apple Account"
   └→ Apple Sign-In dialog appears
   └→ User authenticates with Apple
   └→ Apple User ID stored with Firebase UID abc123
   └→ Reverse lookup created: Apple ID → abc123

3. Next time user signs in with Apple
   └→ App looks up Apple ID in account_links
   └→ Finds primary UID: abc123
   └→ Signs in to Firebase with Apple
   └→ Loads user data from UID abc123
   └→ Google Calendar API still works (tokens stored)
```

### Scenario 2: User Creates Account with Apple, Links Google Later

```
1. User signs in with Apple
   └→ Firebase creates user with UID: xyz789
   └→ Primary account established
   └→ No Calendar access yet (no Google tokens)

2. User clicks "Link Google Account for Calendar"
   └→ Google OAuth flow initiated
   └→ User authenticates with Google
   └→ Google OAuth tokens stored with Firebase UID xyz789
   └→ Reverse lookup created: Google email → xyz789

3. User now has Calendar access
   └→ Google tokens available
   └→ Can access Google Calendar API
   └→ Can sign in with either Apple or Google

4. Next time user signs in with Google
   └→ App looks up Google email in account_links
   └→ Finds primary UID: xyz789
   └→ Signs in to Firebase with Google
   └→ Loads user data from UID xyz789
```

## Firestore Data Structure

### Collection: `users/{firebaseUid}`

```javascript
{
  "email": "user@gmail.com",
  "displayName": "John Doe",
  "role": "admin",
  "linkedAccounts": {
    "google": {
      "email": "john@gmail.com",
      "refreshToken": "encrypted_token_here", // TODO: Encrypt in production
      "accessToken": "current_access_token",
      "linkedAt": Timestamp,
      "provider": "google.com"
    },
    "apple": {
      "userId": "000763.df9a7636b0d34681bfdc59146a208af8.1720",
      "email": "john@privaterelay.appleid.com",
      "linkedAt": Timestamp,
      "provider": "apple.com"
    }
  }
}
```

### Collection: `account_links/{appleUserId}` (for Apple lookups)

```javascript
{
  "primaryFirebaseUid": "abc123",
  "primaryEmail": "user@gmail.com",
  "primaryProvider": "google.com",
  "appleUserId": "000763.df9a7636b0d34681bfdc59146a208af8.1720",
  "appleEmail": "user@privaterelay.appleid.com",
  "linkedAt": Timestamp,
  "linkingMethod": "manual_to_apple"
}
```

### Collection: `account_links/google_{email}` (for Google lookups)

```javascript
{
  "primaryFirebaseUid": "xyz789",
  "primaryEmail": "user@privaterelay.appleid.com",
  "primaryProvider": "apple.com",
  "googleEmail": "john@gmail.com",
  "linkedAt": Timestamp,
  "linkingMethod": "manual_to_google"
}
```

## Implementation Steps

### Step 1: Modify `linkAppleAccount()` in AuthService

Replace the existing Firebase `linkWithCredential` code with manual linking:

```dart
Future<void> linkAppleAccount() async {
  final accountLinking = AccountLinkingService();
  
  try {
    // Get Apple credential
    final appleCredential = await SignInWithApple.getAppleIDCredential(
      scopes: [
        AppleIDAuthorizationScopes.email,
        AppleIDAuthorizationScopes.fullName,
      ],
      nonce: nonce,
    );
    
    // Manual link instead of Firebase linkWithCredential
    await accountLinking.linkAppleToCurrentUser(
      appleUserId: appleCredential.userIdentifier,
      appleEmail: appleCredential.email ?? _auth.currentUser?.email ?? '',
    );
    
    debugPrint('✅ Apple account linked successfully');
  } catch (e) {
    debugPrint('Error linking Apple account: $e');
    rethrow;
  }
}
```

### Step 2: Add "Link Google Account" Flow for Apple Users

When a user is signed in with Apple and wants Calendar access:

```dart
Future<void> linkGoogleAccountForCalendar() async {
  final accountLinking = AccountLinkingService();
  final oauthService = OAuthService();
  
  try {
    // Initiate Google OAuth flow
    await oauthService.signIn();
    
    // Get the OAuth tokens
    final refreshToken = oauthService.refreshToken;
    final accessToken = oauthService.accessToken;
    final googleEmail = oauthService.userEmail;
    
    if (refreshToken == null || googleEmail == null) {
      throw Exception('Failed to get Google OAuth tokens');
    }
    
    // Manual link
    await accountLinking.linkGoogleToCurrentUser(
      googleEmail: googleEmail,
      googleRefreshToken: refreshToken,
      googleAccessToken: accessToken,
    );
    
    debugPrint('✅ Google account linked for Calendar access');
  } catch (e) {
    debugPrint('Error linking Google account: $e');
    rethrow;
  }
}
```

### Step 3: Handle Sign-In with Linked Accounts

Modify the sign-in flows to check for linked accounts:

```dart
// In signInWithApple()
Future<Map<String, String>> signInWithApple() async {
  final accountLinking = AccountLinkingService();
  
  // Get Apple credential
  final appleCredential = await SignInWithApple.getAppleIDCredential(...);
  
  // Check if this Apple ID is linked to an existing account
  final primaryUid = await accountLinking.findPrimaryAccountForApple(
    appleCredential.userIdentifier
  );
  
  if (primaryUid != null) {
    debugPrint('Found existing linked account: $primaryUid');
    // Sign in to Firebase with Apple, but use primary account data
    // Load user profile from primaryUid, not the new Firebase user
  }
  
  // Continue with normal Apple sign-in flow...
}
```

```dart
// In signInWithGoogle()
Future<void> signIn() async {
  final accountLinking = AccountLinkingService();
  
  // Get Google account
  final googleAccount = await _googleSignIn.signIn();
  
  if (googleAccount != null) {
    // Check if this Google account is linked to an existing account
    final primaryUid = await accountLinking.findPrimaryAccountForGoogle(
      googleAccount.email
    );
    
    if (primaryUid != null) {
      debugPrint('Found existing linked account: $primaryUid');
      // Use primary account data
    }
    
    // Continue with normal Google sign-in flow...
  }
}
```

### Step 4: Load Google OAuth Tokens for Calendar Access

When initializing Calendar service:

```dart
Future<void> initializeCalendarService() async {
  final accountLinking = AccountLinkingService();
  final currentUser = FirebaseAuth.instance.currentUser;
  
  if (currentUser == null) return;
  
  // Check which provider the user signed in with
  final provider = currentUser.providerData.first.providerId;
  
  if (provider == 'apple.com') {
    // User signed in with Apple, need to load Google tokens
    final linkedGoogle = await accountLinking.getLinkedGoogleAccount();
    
    if (linkedGoogle != null) {
      // Initialize OAuthService with stored tokens
      await _oauthService.restoreSession(
        email: linkedGoogle['email'],
        refreshToken: linkedGoogle['refreshToken'],
        accessToken: linkedGoogle['accessToken'],
      );
      debugPrint('✅ Calendar access restored from linked Google account');
    } else {
      debugPrint('⚠️ No Google account linked - Calendar access unavailable');
      debugPrint('User needs to link a Google account for Calendar features');
    }
  }
  // If signed in with Google, tokens are already available
}
```

## UI Implementation

### Admin Profile Screen Changes

Add a section to show linked accounts status:

```dart
// Show current sign-in method
Text('Signed in with: ${_getCurrentProvider()}'),

// Show linked accounts
if (_hasLinkedApple)
  ListTile(
    title: Text('Apple Account'),
    subtitle: Text(_appleEmail),
    trailing: Icon(Icons.check_circle, color: Colors.green),
  ),

if (_hasLinkedGoogle)
  ListTile(
    title: Text('Google Account'),
    subtitle: Text(_googleEmail),
    trailing: Icon(Icons.check_circle, color: Colors.green),
  ),

// Show linking buttons
if (!_hasLinkedApple)
  ElevatedButton(
    onPressed: _linkAppleAccount,
    child: Text('Link Apple Account'),
  ),

if (!_hasLinkedGoogle)
  ElevatedButton(
    onPressed: _linkGoogleAccount,
    child: Text('Link Google Account for Calendar'),
  ),
```

### Sign-In Screen Changes

```dart
// Show both options
SignInButton(
  buttonType: ButtonType.google,
  onPressed: () => _signInWithGoogle(),
),

SizedBox(height: 16),

SignInButton(
  buttonType: ButtonType.apple,
  onPressed: () => _signInWithApple(),
),

// Help text
Text(
  'You can link both accounts after signing in',
  style: TextStyle(fontSize: 12, color: Colors.grey),
),
```

## Security Considerations

### 1. Encrypt OAuth Refresh Tokens

```dart
// TODO: Implement token encryption before storing
import 'package:encrypt/encrypt.dart';

String encryptToken(String token) {
  final key = Key.fromSecureRandom(32);
  final iv = IV.fromSecureRandom(16);
  final encrypter = Encrypter(AES(key));
  return encrypter.encrypt(token, iv: iv).base64;
}

String decryptToken(String encryptedToken) {
  // Decrypt using stored key
}
```

### 2. Firestore Security Rules

```javascript
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    
    // Users can only read/write their own data
    match /users/{userId} {
      allow read, write: if request.auth.uid == userId;
    }
    
    // Account links - read only by authenticated users
    match /account_links/{linkId} {
      allow read: if request.auth != null;
      allow write: if request.auth != null && 
                      (resource.data.primaryFirebaseUid == request.auth.uid ||
                       !exists(/databases/$(database)/documents/account_links/$(linkId)));
    }
  }
}
```

### 3. Token Rotation

Implement automatic refresh token rotation:

```dart
Future<void> rotateGoogleToken() async {
  // Periodically refresh and re-store Google OAuth tokens
  await _oauthService.refreshAccessToken();
  await accountLinking.linkGoogleToCurrentUser(
    googleEmail: _oauthService.userEmail!,
    googleRefreshToken: _oauthService.refreshToken!,
    googleAccessToken: _oauthService.accessToken!,
  );
}
```

## Testing Checklist

### Scenario 1: Google First, Then Apple
- [ ] Create account with Google
- [ ] Verify Calendar access works
- [ ] Link Apple account
- [ ] Sign out
- [ ] Sign in with Apple
- [ ] Verify same user data loads
- [ ] Verify Calendar access still works

### Scenario 2: Apple First, Then Google
- [ ] Create account with Apple
- [ ] Verify no Calendar access
- [ ] Link Google account
- [ ] Verify Calendar access now works
- [ ] Sign out
- [ ] Sign in with Google
- [ ] Verify same user data loads
- [ ] Verify Calendar access works

### Edge Cases
- [ ] Different emails for Google and Apple
- [ ] Hidden Apple email (privaterelay)
- [ ] Account already linked error handling
- [ ] Unlink and re-link accounts
- [ ] Multiple devices with same account

## Migration Plan for Existing Users

For users who already have Google-only accounts:

1. Deploy the manual linking code
2. Show a banner: "Link your Apple account for App Store compliance"
3. Users can optionally link Apple
4. No disruption to existing functionality

## App Store Compliance

This implementation satisfies Apple's requirement:

✅ **4.8 Sign in with Apple**
> Apps that use a third-party or social login service (such as Google Sign-In) to set up or authenticate the user's primary account must also offer Sign in with Apple as an equivalent option.

The app:
- Offers both Google and Apple Sign-In
- Treats them equally (both can be primary)
- Allows account linking
- Users can choose either method

## Future Enhancements

1. **Multiple Google Accounts**: Allow linking multiple Google accounts for different calendars
2. **Account Merging**: UI to merge two separate accounts if user accidentally creates both
3. **Social Graph**: Show which accounts are linked in admin dashboard
4. **Audit Log**: Track all linking/unlinking operations
5. **Token Encryption**: Implement proper encryption for stored OAuth tokens

---

**Status**: Ready for implementation
**Priority**: High - Required for App Store submission
**Estimated Effort**: 4-6 hours implementation + 2-3 hours testing
