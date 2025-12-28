# Athlete Registration Form & Waiver System

## Overview

The BPVA Calendar app now includes a comprehensive document management system for athletes to submit required registration forms and liability waivers. This system provides a complete workflow for document upload, review, approval/rejection, and status tracking.

## Key Features

### For Athletes
- Upload registration forms and waivers (PDF, JPG, PNG formats)
- View document submission status (pending, approved, rejected, not submitted)
- Receive feedback from admins when documents are rejected
- Resubmit rejected documents with corrections
- Track completion progress in profile

### For Admins
- Review pending documents from all athletes
- Approve or reject documents with notes/feedback
- View document statistics and history
- Access all athlete documents in centralized dashboard
- Filter by pending, approved, or rejected status

### For Parents
- View linked athlete's document status
- Read-only access to athlete's uploaded documents

## Architecture

### Data Model

**AthleteDocument** (`lib/models/athlete_document.dart`)
- Document metadata (type, status, timestamps)
- File information (URL, name, type, size)
- Review information (reviewer, notes, review date)
- Athlete information (userId, email, name)

**Document Types:**
- `registrationForm` - BPVA registration form with personal info and emergency contacts
- `waiver` - Liability waiver acknowledging pole vault training risks

**Document Status:**
- `notSubmitted` - Athlete hasn't uploaded yet
- `pending` - Uploaded and awaiting admin review
- `approved` - Admin approved the document
- `rejected` - Admin rejected (requires resubmission with notes explaining why)

### Services

**DocumentService** (`lib/services/document_service.dart`)

Singleton service following the app's standard service pattern with:
- File upload to Firebase Storage
- Document metadata management in Firestore
- Document status tracking
- Admin review operations (approve/reject)
- Statistics and reporting

**Key Methods:**
```dart
// Athlete operations
Future<List<AthleteDocument>> getAthleteDocuments(String userId)
Future<AthleteDocument> submitDocument({...})
Future<Map<String, bool>> getDocumentCompletionStatus(String userId)

// Admin operations
Future<List<AthleteDocument>> getPendingDocuments()
Future<void> approveDocument({...})
Future<void> rejectDocument({...})
Future<Map<String, int>> getDocumentStatistics()
```

### UI Screens

**AthleteDocumentUploadScreen** (`lib/screens/user/athlete_document_upload_screen.dart`)

Athlete-facing screen for managing documents:
- Card-based UI showing status of each required document
- Upload button with file picker (PDF, JPG, PNG)
- View uploaded documents
- Resubmit rejected documents
- Display rejection reasons from admins

**AdminDocumentReviewScreen** (`lib/screens/admin/admin_document_review_screen.dart`)

Admin dashboard for document review:
- Two tabs: Pending Documents and All Documents
- Document statistics (pending, approved, rejected counts)
- Approve/reject actions with notes
- View athlete information and document details
- Filter and sort capabilities

**AthleteMyProfileScreen** (enhanced)

Added "Required Documents" section showing:
- Registration form status with checkmark/warning icon
- Waiver status with checkmark/warning icon
- Overall completion indicator
- "Manage Documents" button to upload screen

## Firebase Configuration

### Firestore Collections

**athlete_documents**
```
/athlete_documents/{documentId}
  - documentId: string
  - userId: string (athlete's Firebase UID)
  - athleteEmail: string
  - athleteName: string
  - documentType: 'registrationForm' | 'waiver'
  - status: 'pending' | 'approved' | 'rejected' | 'notSubmitted'
  - fileUrl: string (Firebase Storage download URL)
  - fileName: string
  - fileType: string (MIME type)
  - fileSizeBytes: number
  - submittedAt: timestamp
  - reviewedAt: timestamp
  - reviewedByUserId: string
  - reviewedByName: string
  - reviewNotes: string
  - createdAt: timestamp
  - updatedAt: timestamp
```

### Firestore Security Rules

Added to `firestore.rules`:
```javascript
match /athlete_documents/{documentId} {
  // Athletes can read/write their own documents
  allow read: if request.auth.uid == resource.data.userId;
  allow create: if request.auth.uid == request.resource.data.userId;
  allow update: if request.auth.uid == resource.data.userId &&
                   (resource.data.status == 'pending' || 
                    resource.data.status == 'rejected');
  allow delete: if request.auth.uid == resource.data.userId;
  
  // Parents can read linked athletes' documents
  allow read: if request.auth.uid in 
                 firestore.get(/databases/$(database)/documents/user_memberships/$(request.auth.uid))
                 .data.linkedAthleteIds;
  
  // Admins can read all and update status (approve/reject)
  allow read, update: if isAdminUser();
}
```

### Firebase Storage

**Storage Path:** `athlete_documents/{userId}/{documentType}_{timestamp}.{ext}`

**Storage Security Rules** (`storage.rules`):
```javascript
match /athlete_documents/{userId}/{fileName} {
  // Athletes can upload their own documents (max 10MB, PDF/images only)
  allow write: if request.auth.uid == userId &&
                  request.resource.size < 10 * 1024 * 1024 &&
                  (request.resource.contentType.matches('application/pdf') ||
                   request.resource.contentType.matches('image/.*'));
  
  // Athletes, parents, and admins can read
  allow read: if request.auth.uid == userId ||
                 userId in firestore.get(/databases/(default)/documents/user_memberships/$(request.auth.uid))
                 .data.linkedAthleteIds ||
                 isAdminUser();
}
```

## User Workflow

### Athlete Journey

1. **Initial State**
   - Athlete signs up and creates account
   - Profile shows "Required Documents" card with warnings
   - Both documents show "Not Submitted" status

2. **Upload Registration Form**
   - Tap "Manage Documents" button
   - Select "Upload" for Registration Form
   - Choose PDF or image file from device
   - File uploads to Firebase Storage
   - Status changes to "Pending Review"

3. **Upload Waiver**
   - Same process for waiver document
   - Both documents now pending

4. **Admin Review**
   - Admin reviews in admin dashboard
   - **If Approved:** Status changes to "Approved", green checkmark shows
   - **If Rejected:** Status changes to "Rejected", admin provides reason

5. **Resubmission (if rejected)**
   - Athlete sees rejection reason in red alert box
   - "Resubmit" button appears
   - Upload corrected document
   - Status changes back to "Pending Review"

6. **Completion**
   - Both documents approved
   - Profile shows green "All required documents completed!" banner
   - Athlete is fully registered

### Admin Workflow

1. **Access Review Screen**
   - Navigate to Admin Dashboard
   - Select "Document Review" option
   - See pending documents count in tab badge

2. **Review Pending Document**
   - Tap on document card to expand details
   - View athlete name, email, document type
   - Tap "View" to open document in browser/viewer

3. **Approve Document**
   - Tap "Approve" button
   - Optionally add approval notes
   - Document marked as approved
   - Removed from pending list

4. **Reject Document**
   - Tap "Reject" button
   - **Required:** Enter rejection reason
   - Document status changes to rejected
   - Athlete receives feedback

5. **Monitor Statistics**
   - View overall stats: total, pending, approved, rejected
   - Track document types: registration forms vs waivers
   - Use "All Documents" tab to see history

## File Type Support

**Supported Formats:**
- **PDF** (`.pdf`) - Recommended for scanned forms
- **JPEG** (`.jpg`, `.jpeg`) - Photos of signed forms
- **PNG** (`.png`) - Screenshots or digital signatures

**File Size Limit:** 10MB per document

**Storage Location:** Firebase Storage bucket `athlete_documents/`

## Integration with Existing Features

### User Management
- Document status can be viewed in athlete details screen
- Parents linked to athletes can view document status
- Admin user management screen could show document completion indicator

### Gym Check-In
- Could add requirement that documents must be completed before check-in
- Display warning during check-in if documents incomplete

### Membership Management
- Add document completion to membership requirements
- Track which athletes have incomplete paperwork
- Send reminders for missing documents

## Future Enhancements

### Planned Features
1. **Email Notifications**
   - Notify admins when new documents submitted
   - Notify athletes when documents reviewed
   - Reminder emails for incomplete documents

2. **In-App PDF Viewer**
   - View PDFs without leaving app
   - Zoom and annotation support
   - Signature capture for digital signing

3. **Document Templates**
   - Downloadable blank forms
   - Pre-fill with athlete information
   - Digital signature fields

4. **Expiration Tracking**
   - Set expiration dates for waivers (e.g., annual renewal)
   - Automatic reminders before expiration
   - Bulk renewal notifications

5. **Parent Signatures**
   - For minor athletes (under 18)
   - Require parent to sign waiver
   - Parent approval workflow

6. **Batch Operations**
   - Approve/reject multiple documents at once
   - Export document list to CSV
   - Print document checklists

7. **Mobile Scanning**
   - Use camera to scan physical forms
   - Auto-crop and enhance
   - Convert to PDF automatically

## Testing

### Unit Tests (to be created)
```dart
// Test document service operations
test/services/document_service_test.dart

// Test document model serialization
test/models/athlete_document_test.dart
```

### Integration Tests (to be created)
```dart
// Test upload workflow
test/integration/document_upload_test.dart

// Test admin review workflow
test/integration/document_review_test.dart
```

### Manual Testing Checklist

**Athlete Tests:**
- [ ] Upload registration form (PDF)
- [ ] Upload registration form (JPEG)
- [ ] Upload waiver (PDF)
- [ ] View uploaded document
- [ ] See pending status after upload
- [ ] See rejection reason
- [ ] Resubmit rejected document
- [ ] See approved status
- [ ] Profile shows correct completion status

**Admin Tests:**
- [ ] View pending documents
- [ ] Approve document with notes
- [ ] Approve document without notes
- [ ] Reject document (requires notes)
- [ ] View all documents
- [ ] View document statistics
- [ ] View athlete details from document

**Parent Tests:**
- [ ] View linked athlete's document status
- [ ] Cannot modify athlete's documents

## Deployment Checklist

1. **Install Dependencies**
   ```bash
   flutter pub get
   ```

2. **Deploy Firestore Rules**
   ```bash
   firebase deploy --only firestore:rules
   ```

3. **Deploy Storage Rules**
   ```bash
   firebase deploy --only storage
   ```

4. **Build and Test**
   ```bash
   # Test on dev environment
   ./run.sh dev
   
   # Build for staging
   ./build.sh staging
   
   # Build for production
   ./build.sh prod
   ```

5. **Verify Firebase Console**
   - Check Firestore collection created on first upload
   - Verify Storage bucket has correct permissions
   - Test file upload/download URLs work

## Troubleshooting

### Common Issues

**"Error uploading document"**
- Check file size (must be under 10MB)
- Verify file type is PDF, JPG, or PNG
- Ensure athlete is signed in
- Check Firebase Storage rules deployed

**"Cannot view document"**
- Verify Storage rules allow read access
- Check file URL is valid
- Ensure browser/app can open file type

**"Permission denied"**
- Verify Firestore rules deployed correctly
- Check user is authenticated
- Confirm userId matches document owner

**Documents not showing in admin panel**
- Check admin user has proper role
- Verify Firestore query permissions
- Ensure documents exist in Firestore

## Code Locations

**Models:** `lib/models/athlete_document.dart`

**Services:** `lib/services/document_service.dart`

**Athlete Screens:**
- `lib/screens/user/athlete_document_upload_screen.dart`
- `lib/screens/user/athlete_my_profile_screen.dart` (enhanced)

**Admin Screens:**
- `lib/screens/admin/admin_document_review_screen.dart`

**Firebase Configuration:**
- `firestore.rules` (Firestore security)
- `storage.rules` (Storage security)
- `firebase.json` (deployment config)

## Dependencies Added

**pubspec.yaml:**
```yaml
dependencies:
  firebase_storage: ^12.4.0  # File upload/download
  file_picker: ^10.3.7       # Already present - file selection
  url_launcher: ^6.2.1       # Already present - open documents
```

## Support

For issues or questions about the document system:
1. Check this README first
2. Review Firebase Console for errors
3. Check app logs for detailed error messages
4. Contact development team with specific error details
