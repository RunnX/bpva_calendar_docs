# Deployment Guide

This document explains how to deploy and configure the BPVA Calendar App documentation site using GitHub Pages.

## Quick Start

The site is automatically deployed via GitHub Pages. Once the repository settings are configured, changes pushed to the `main` branch will automatically rebuild and deploy the site.

## GitHub Pages Configuration

To enable GitHub Pages for this repository:

1. Go to the repository on GitHub
2. Click on **Settings** → **Pages** (in the left sidebar)
3. Under "Source", select:
   - **Branch**: `main`
   - **Folder**: `/ (root)`
4. Click **Save**

GitHub Pages will automatically:
- Detect the Jekyll configuration in `_config.yml`
- Build the site using Jekyll
- Deploy to: `https://runnx.github.io/bpva_calendar_docs`

## Build Time

- Initial deployment: 1-2 minutes
- Subsequent updates: 30-60 seconds

## Viewing the Site

Once deployed, the site will be available at:
**https://runnx.github.io/bpva_calendar_docs**

## Custom Domain (Optional)

To use a custom domain:

1. Add a `CNAME` file to the repository root with your domain name
2. Configure DNS settings with your domain provider:
   - Add a CNAME record pointing to `runnx.github.io`
3. In GitHub Settings → Pages, enter your custom domain
4. Enable "Enforce HTTPS"

## Local Development

To preview the site locally before pushing changes:

### Prerequisites
- Ruby 2.5 or higher
- Bundler and Jekyll gems

### Setup
```bash
# Install Jekyll
gem install bundler jekyll

# Clone the repository
git clone https://github.com/RunnX/bpva_calendar_docs.git
cd bpva_calendar_docs

# Build and serve the site
jekyll serve

# View at http://localhost:4000
```

### Making Changes
1. Edit the `.md` files in the repository root
2. Jekyll will auto-reload (if using `--livereload` flag)
3. Preview changes in your browser
4. Commit and push when satisfied

## File Structure

```
bpva_calendar_docs/
├── _config.yml           # Jekyll configuration
├── index.md              # Home page
├── getting-started.md    # Getting Started guide
├── features.md           # Features overview
├── support.md            # Support and FAQ
├── privacy.md            # Privacy Policy
├── terms.md              # Terms of Service
├── README.md             # Repository README
├── .gitignore           # Git ignore rules
└── DEPLOYMENT.md        # This file
```

## Troubleshooting

### Site not updating
- Wait 1-2 minutes for GitHub Pages to rebuild
- Check the Actions tab for build status
- Verify the correct branch is configured in Settings

### Build errors
- Check that all Markdown files have valid front matter
- Verify `_config.yml` syntax is correct
- Review the build logs in the Actions tab

### Theme not loading
- GitHub Pages automatically installs the theme specified in `_config.yml`
- The Cayman theme is natively supported by GitHub Pages
- No additional configuration needed

## Supported Themes

GitHub Pages supports these themes out of the box:
- jekyll-theme-cayman (current)
- jekyll-theme-minimal
- jekyll-theme-slate
- jekyll-theme-primer
- And others...

To change themes, edit the `theme:` line in `_config.yml`.

## Additional Resources

- [GitHub Pages Documentation](https://docs.github.com/en/pages)
- [Jekyll Documentation](https://jekyllrb.com/docs/)
- [Markdown Guide](https://www.markdownguide.org/)
- [Jekyll Themes](https://pages.github.com/themes/)

## Support

For questions about deployment, please file an issue in this repository.
