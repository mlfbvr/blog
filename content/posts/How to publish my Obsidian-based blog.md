---
title: How to publish my Obsidian-based blog
date: 2025-12-16
draft: false
tags:
  - blog
---
```
$ rsync -a --delete --info=NAME <path to blog subdir in Obsidian vault> <path to hugo-managed site>/content/posts/
$ cd <hugo-managed site>
$ hugo
$ git add .
$ git commit -m "<Commit Message>"
$ git push
$ git subtree split --prefix public -b public_site
$ git push origin public_site:public_site --force
$ git branch -D public_site 

```