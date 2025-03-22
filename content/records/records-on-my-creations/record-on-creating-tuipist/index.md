---
draft: true

title: 'Creating Tuipist'
subtitle: 'Why, & how'
summary: 'This record is an account of my experience building, and improving my command line typing practice tool - Tuipist.'
description: 'Reasons to create Tuipist, and features.'

date: 2024-09-21T18:04:39-07:00
lastMod: 2024-09-26T00:47:08-07:00

author: "fenrir"
authorLink: "https://fenrir.is-a.dev"
license: "<a rel='license external nofollow noopener noreffer' href='https://opensource.org/licenses/GPL-3.0' target='_blank'>GPL-3.0</a>"

tags: ["tui", "terminal", "typing", "go"]
categories: ["deepdive"]
relative: true

featuredImage: "images/featured-image.png"
featuredImagePreview: "images/featured-image.png"

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

{{< admonition type=info title="Fun Fact" open=false >}}
A **tip** banner
{{< /admonition >}}

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
- [ ] Lessons from beginner to advanced
- [ ] Keyboard layout emulation
- [ ] Fun exercises
- [ ] A good selection of settings
- [ ] WPM history tracking

## The Design

Rust's RataTUI was something I considered to make the project with, but decided to use
Go instead. I settled on using the Bubbletea and Lipgloss packages by the charmbracelet
team.

I very much like the practice pane of Monkeytype. I decided to keep to it as much as 
possible to avoid cluttering the main page. The practice text needed to be in the center
of the page. The optional keyboard should be below the text, and I decided to give the 
user options to change the size of both the text and the keyboard.

To keep the UI minimal and create as little distraction as possible, only the active tab 
would be highlighted. Or the inactive tab would be dimmed.

For the settings, initially, I stuck to the basics - keyboard layout, inclusion of
punctuations, letters and modifier keys.

## The Code

```go {open=true, title="main.go"}
func (m model) Update() {
    return nil
}
```
