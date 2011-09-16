---
layout: post
title: "Vim - Fit Text Into 80 Columns"
date: 2011-09-16 14:24
comments: true
categories: Vim
---

I often use this trick when I'm writing long emails and need to do some editing
that pushes a few lines too far out. Just copy the text into Vim and move your
cursor to the beginning (`gg` in command mode). Make sure that textwidth is set
to 80 (`:set textwidth=80`) and then type `gqG`.
