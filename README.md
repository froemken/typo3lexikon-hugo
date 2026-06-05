# TYPO3 Lexikon goes Hugo

This is the base repository of my website [TYPO3 Lexikon](https://www.typo3lexikon.de). I have migrated it from TYPO3 CMS to [Hugo](https://gohugo.io/) in January 2024.

## Advantages

- Use the [GitHub bugtracker](https://github.com/froemken/typo3lexikon-hugo/issues) to ask for features or report issues
- Contribute bug fixes easily
- Read/write using standard Markdown (`*.md` files)
- Improved code highlighting
- Faster navigation and outline on long pages
- No database management or complex upgrades
- Hostable on simple Apache servers (utilizing `.htaccess`)

## Usage & License

### Personal and Business Use

You can copy excerpts of the documentation and paste them as a quote, as long as you link back to [TYPO3 Lexikon](https://www.typo3lexikon.de). Copying the complete content of a page or the entire site is not permitted.

### TYPO3 CMS Community & Documentation Team

Individuals, groups, and companies involved with the [TYPO3 Association](https://typo3.org/project/association), [TYPO3 GmbH](https://typo3.com/), or official [TYPO3 Teams](https://typo3.org/community/teams) are permitted to copy, modify, store, and translate the complete content, navigation, and structure for use on official TYPO3 domains (`typo3.*` and subdomains). No backlinks, citations, or attribution are required.

## Local Development & Testing

> **Note**
> The build and sync commands below are for **local testing and manual override only**. Production deployment is fully automated on push to the `main` branch via GitHub Actions.

### Running a Local Server

Start a hot-reloading local development server:
```shell
hugo server
```

Once started, the local preview of the website is available in your browser at [http://localhost:1313/](http://localhost:1313/).

### Local Build & Manual Sync

To compile the static pages locally and optionally synchronize them manually (Note: remote server does not support `rsync` directly, so manual deployments should use SFTP/lftp):
```shell
# Generate optimized HTML files locally into the /public folder
hugo --gc --cleanDestinationDir --minify

# Sync locally built files (adjust path as needed)
rsync -avz --delete public/ [server]:~/typo3cms/[path]
```
