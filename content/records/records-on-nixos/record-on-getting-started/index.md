---
draft: true

title: 'Getting Started With NixOS'
subtitle: "A gentle introduction to NixOS"
summary: "This is a record of my thoughts on why one should switch to NixOS, and how to get started with it."
description: "Installing NixOS and basic configuration."

date: 2024-09-21T01:06:33-07:00

author: "raikan-san"
authorLink: "https://raikan-san.is-a.dev"
license: "<a rel='license external nofollow noopener noreffer' href='https://opensource.org/licenses/GPL-3.0' target='_blank'>GPL-3.0</a>"

tags: ["nix", "nixos"]
categories: ["guide"]
relative: true

featuredImage: "/images/featured-image.png"
featuredImagePreview: "/images/featured-image.png"

images: ["/records-on-nixos/record-on-getting-started/images/featured-image.png"]
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

Linux has many many many (and many more) distributions. Needless to say, you have
a lot of options to choose from. NixOS is one of those choices and in this article
I will articulate my thought on why it is one of the better ones.

## What is Nix?

All Linux distributions use a package manager to manage installed software on the system.
Debian based distros use `apt`, Arch based systems use `pacman`, Fedora uses `dnf` and
openSUSE uses `zypper` for package management. These package managers require you to 
imperatively install packages that you would like to use, either through an app store
or the terminal. 

The `nix` package manager however, allows you to declaratively manage your packages in a 
reproducible manner. This means that every single package can be declared in a file, and 
the package is installed based on the configuration. You could even take the same file
to another system that has the `nix` package manager install all the software used in the
first system through a single command. 

{{< admonition type=note title="Fun fact" open=false >}}
The `nix` package manager has the most packages under it's belt. It has around 55,000
fresh packages which is about double that of the ArchLinux User Repository!
{{< /admonition >}}

## What is NixOS?

NixOS is a free and open source Linux distribution based on the nix package manager.
What makes it unique in the sea of available distributions is that the system can be
configured in the same manner you would configure packages in the `nix` package manager.

Imagine that!! Declaratively configuring your system in a functional and reproducible 
manner!
