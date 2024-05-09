# TYPO3 Lexikon goes Hugo

This is the base repository of my website [TYPO3 Lexikon](https://www.typo3lexikon.de). I have migrated it from TYPO3 CMS to [Hugo](https://gohugo.io/) in January 2024.

## Advantages:

- You can use the GitHub bugtracker to ask for feature requests
- You can contribute bug fixes
- Almost anyone can read/write `*.md` files (MarkDown)
- Better code highlighting
- I find sections much faster on long pages
- No need to update/upgrade anything. More time for writing documentation.
- Project can be hosted on servers with just apache (needed because of `.htaccess`)

## Usage

### Personal and business

You can copy a few lines of my documentation and paste them as a quote with backlink to [TYPO3 Lexikon](https://www.typo3lexikon.de).

Copying full content of one site or all sites is not allowed.

### TYPO3 CMS related

People, groups, companies involved with:

- [TYPO3 Association](https://typo3.org/project/association)
- [TYPO3 GmbH](https://typo3.com/)
- [TYPO3 teams](https://typo3.org/community/teams) (especially the [TYPO3 documentation team](https://typo3.org/community/teams/documentation))

are allowed to copy, modify, store and translate the complete content with all examples, navigation and structure to websites of the *official* TYPO3 domains (`typo3.*` incl. all subdomains). There is no need to link back to my site, no need to cite the content, no need to include my name as the original author. It would be cool to keep my examples as is, but it's not mandatory.

## Build

```shell
hugo server
hugo --cleanDestinationDir --minify
git add .
git commit
git push origin main
rsync -avz --delete public jweiland65.net:~/typo3cms/hugo
```
