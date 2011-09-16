---
layout: post
title: "Git - How to Remove the Top Commit"
date: 2011-09-15 18:44
comments: true
categories: git
---

Just run:
```
git reset --hard HEAD~1
```
If you haven't pushed, that's all you need to do. If you *have* pushed, and
you're *sure* that no one else has pulled your changes, you could push again
with '-f' (force push):
```
git push -f origin master
```
If, on the other hand, there's a chance that someone has already pulled your
changes, you'll want to use `revert` instead.  This creates a new commit that
backs out your bad commit:
```
git revert HEAD
git push origin master
```
