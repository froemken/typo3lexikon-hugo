# typo3lexikon-hugo

A first try to migrate my typo3lexikon.de website to hugo

```shell
hugo server
hugo --cleanDestinationDir --minify
git add .
git commit
git push origin main
rsync -avz --delete public jweiland65.net:~/typo3cms/hugo
```
