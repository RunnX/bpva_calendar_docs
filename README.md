# BPVA Calendar App Documentation

Official documentation site for the Buckeye Pole Vault Academy Calendar Application.

## 📱 About

This repository contains the GitHub Pages documentation site for the BPVA Calendar App, available on both the Google Play Store and Apple App Store.

## 🌐 View the Documentation

Visit the live documentation site at: [https://runnx.github.io/bpva_calendar_docs](https://runnx.github.io/bpva_calendar_docs)

## 📚 What's Included

- **Getting Started Guide** - Step-by-step setup instructions for new users
- **Features Overview** - Comprehensive list of app capabilities
- **Manual Account Linking** - How the app links Apple and Google accounts
- **Support & FAQ** - Common questions and troubleshooting help
- **Privacy Policy** - How we handle user data
- **Terms of Service** - Usage terms and conditions

## 🔐 Authentication Features

The BPVA Calendar App supports both Google and Apple Sign-In:
- **Google Sign-In** - Required for Google Calendar API integration
- **Apple Sign-In** - App Store requirement, supports "Hide My Email"
- **Manual Account Linking** - Links Apple and Google accounts via Firestore (emails don't need to match)
- **Flexible Authentication** - Sign in with either provider once accounts are linked

## 🛠️ Technical Details

This site is built with:
- **Jekyll** - Static site generator
- **GitHub Pages** - Free hosting
- **Cayman Theme** - Clean, responsive design

## 🚀 Local Development

To run this site locally:

```bash
# Install Jekyll (requires Ruby)
gem install bundler jekyll

# Clone the repository
git clone https://github.com/RunnX/bpva_calendar_docs.git
cd bpva_calendar_docs

# Serve the site locally
jekyll serve

# View at http://localhost:4000
```

## 📝 Contributing

To update the documentation:

1. Edit the Markdown files (`.md` files in the root directory)
2. Test your changes locally with `jekyll serve`
3. Commit and push to the `main` branch
4. GitHub Pages will automatically rebuild and deploy the site

## 📄 License

Copyright © 2025 Buckeye Pole Vault Academy. All rights reserved.
