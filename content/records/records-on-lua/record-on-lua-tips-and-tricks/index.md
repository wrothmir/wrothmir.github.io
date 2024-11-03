---
draft: true

title: 'Lua Tips and Tricks'
subtitle: ''
summary: ''
description: ''

date: 2024-11-02T21:50:34-07:00
lastMod: 2024-11-02T21:50:34-07:00

author: "fenrir"
authorLink: "https://fenrir.is-a.dev"
license: "<a rel='license external nofollow noopener noreffer' href='https://opensource.org/licenses/GPL-3.0' target='_blank'>GPL-3.0</a>"

tags: []
categories: []
relative: true

featuredImage: ""
featuredImagePreview: ""

images: [""]
toc:
  auto: true

hiddenFromHomePage: false
hiddenFromSearch: false
twemoji: false
lightgallery: true
ruby: true
fraction: true
linkToMarkdown: true
rssFullText: true
enableLastMod: true
enableWordCount: true
enableReadingTime: true
---

## Boolean Short Circuiting

```lua
x = (a < b) and a or b
```
