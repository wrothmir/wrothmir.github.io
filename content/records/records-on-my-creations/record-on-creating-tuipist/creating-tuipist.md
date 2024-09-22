---
draft: true

title: 'Creating TUIpist'
subtitle: 'Why, & how'
summary: 'This record is an account of my experience building, and improving my command line typing practice tool - TUIpist.'
description: 'Reasons to create TUIpist, and how I did so.'

date: 2024-09-21T18:04:39-07:00

author: "raikan-san"
authorLink: "https://raikan-san.is-a.dev"
license: "<a rel='license external nofollow noopener noreffer' href='https://opensource.org/licenses/GPL-3.0' target='_blank'>GPL-3.0</a>"

tags: ["tui", "terminal", "typing", "go"]
categories: ["deepdive"]
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
---

# Introduction

I started my journey with keyboard layouts in 2022. The reason I switched from the 
traditional QWERTY layout to a Dvorak layout, was because I thought it would be cool to
be different. Little did I know, there was a community that cared about keyboard layouts,
and much more. So I started digging into it and went on to discover that a good keyboard
layout offers much more than just speed.

A good keyboard layout offers better speed, more flow between letters, and has less keys
so you fingers don't have to flail around trying to get to that one key in the corner. A
good layout will have good rolling between bigrams and provide a better way to access
modifier keys, and make your overall typing experience much more comfortable.

Going down the rabbit hole of trying different keyboard layouts, I never stuck
with any keyboard layout other than Dvorak. But I decided to try out the ISRT layout 
recently. 

Soon, I ran into a problem. There was only one website that could help me practice the
layout - Monkeytype. And while I am almost always connected to the internet, I wanted
a tool that would gradually guide me through familiarizing myself with the layout, and the
bigrams and trigrams, and not just throw me into the rough seas of learning a layout.

I also love living in the terminal. I pretty much do all my work there, and had wanted to
build a TUI application. This situation presented me with a perfect chance to get started.

So with all that out of the way, let's get started.

## The Goals

The checkboxes that I wanted ticked for this project were:
- [ ] A clean UI
- [ ] Lessons to familiarize with the keys
- [ ] Settings to allow changing keyboard layouts
- [ ] Something that keeps track of the words per minute for the user

## The Set-up

For the project, I chose to use Go. I had heard a lot about the bubbletea and lipgloss
packages and wanted to try them out. Rust's RataTUI was also something I considered,
but decided not to go ahead with it.
