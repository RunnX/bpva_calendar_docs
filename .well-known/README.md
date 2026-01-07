# Android App Links Setup for Voice Assistants

This directory contains the Digital Asset Links file required for Android App Links to work with voice assistants (Google Assistant, Bixby, etc.).

## What is this file?

The `assetlinks.json` file verifies that the BPVA Calendar Android app is authorized to open links from the `bpva.runnx.com` domain.

## Setup Instructions

### 1. Get Your SHA256 Fingerprint

The easiest way is from Google Play Console:

1. Go to [Google Play Console](https://play.google.com/console)
2. Select your app (BPVA Calendar)
3. Navigate to: **Setup → App Integrity**
4. Under "App signing key certificate", find the **SHA256 certificate fingerprint**
5. Copy the value (it's a long hexadecimal string without colons)

### 2. Update assetlinks.json

Edit the file `docs/.well-known/assetlinks.json` and replace:

```
REPLACE_WITH_YOUR_SHA256_FROM_PLAY_CONSOLE
```

with your actual SHA256 fingerprint.

### 3. Commit and Push

```bash
git add docs/.well-known/assetlinks.json
git commit -m "Configure Digital Asset Links for Android App Links"
git push
```

GitHub Pages will automatically deploy the file to:
```
https://bpva.runnx.com/.well-known/assetlinks.json
```

### 4. Verify Deployment

After pushing, verify the file is accessible:

```bash
curl https://bpva.runnx.com/.well-known/assetlinks.json
```

You should see your JSON configuration with the SHA256 fingerprint.

### 5. Test on Device

```bash
# Test the deep link
adb shell am start -a android.intent.action.VIEW -d "https://bpva.runnx.com/frank?query=test"

# Check verification status
adb shell pm get-app-links com.runnx.bpva
```

The domain should show as "verified" after the app is installed.

## How it Works

When a user says **"Hey Google, ask Frank about poles"**:

1. Google Assistant recognizes the voice command
2. Checks if any app can handle `https://bpva.runnx.com/frank`
3. Verifies ownership by fetching this `assetlinks.json` file
4. If verified, opens the BPVA Calendar app directly to Frank
5. Passes the query to the chatbot

## Troubleshooting

**File not accessible?**
- GitHub Pages may take a few minutes to deploy
- Check that the file is in the `main` branch (or your GitHub Pages source branch)
- Verify GitHub Pages is enabled in repository settings

**Domain not verified?**
- SHA256 must match exactly (no colons, no spaces)
- Reinstall the app after updating assetlinks.json
- Check Play Console → App Integrity to confirm the fingerprint

**Voice assistant not working?**
- App must be published to Play Store (internal testing is sufficient)
- Wait 24 hours for Google to index the App Actions
- Check Play Console → App Actions for validation errors

## Additional Resources

- [Android App Links Documentation](https://developer.android.com/training/app-links)
- [Digital Asset Links](https://developers.google.com/digital-asset-links)
- [App Actions Documentation](https://developers.google.com/assistant/app/get-started)
