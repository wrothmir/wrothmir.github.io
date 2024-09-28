---
draft: false

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
Source: https://devops.com/nixpkgs-how-to-build-better-more-open-software/
{{< /admonition >}}

## What is NixOS?

NixOS is a free and open source Linux distribution based on the nix package manager.
What makes it unique in the sea of available distributions is that the system can be
configured in the same manner you would configure packages in the `nix` package manager.

Imagine that!! Declaratively configuring your system in a functional and reproducible 
manner! You can configure your own hardware, software, services, and so much more,
giving an easy way to control your system.

Nix also allows you to create and manage development environments through `nix-shell` 
which means you can do away with Docker.

One of the biggest advantages of `nix` is that if you ever have a system crash due to
any reason whatsover, and are unable to get into your system, you can always roll back
to a previous version of your system. This gives you the freedom to experiment without
the worry that you might end up having to reinstall your operating system and setting it
up all over again.

The one con against all it's pros is that this reproducibility and customizability comes
at the cost of a steep learning curve, which I belive is a barrier that stops many 
from either trying it out or sticking with NixOS long enough to feel the benefits. This is
made worse at times by the lack of documentation and learning resources. But with more 
learning resources coming up, that disadvantage no longer remains.

## Installing NixOS

To install NixOS, you need a bootable drive with the NixOS iso image on it. Installing 
is as simple as plugging in the drive and booting the system from it. You can then
follow the installation prompts and you will have your NixOS system in a few minutes. You
will need an internet connection for the setup to complete.

{{< admonition type=question title="Installation stuck at 46%?" open=false >}}
Fret not, I ran into this myself, and so have almost all the users of NixOS. Give the 
system some time and it will jump straight to a 100!
{{< /admonition >}}

Reboot your system after removing the boot drive and you can start playing around with
your system configuration.

## Searching nix packages

After installing nixos, you need to add packages that you wish to install. First you
need to find the name of the package as is referenced in the nix repository. You 
can do this in a few ways.

1. Using the website 

    You can open the website at [*search.nixos.org*](https://search.nixos.org) an search 
for any packages that you wish to install. This does require an internet connection.

2. Using the `nix-env -qaP` command

    If you want to look up packages without an internet connection, you can follow this
method, though it is slow and takes up a lot of memory. Once the list is open, you can
search for the package by typing `/` followed by the package name. To exit, press *q*.

3. Using the nix REPL

    Start the nix REPL using the command `nix repl`. Then type `:load <nixpkgs>` when the
prompt for nix repl appears. This will load up all the packages in the nix repository.
Type the name of the pagkage you want to search and use tab completion to see the
available options.

## Installing packages

Once you have the name of the package you want to install, open up the file at
`/etc/nixos/configuration.nix` with admin priviledges using the following command:

```bash
sudo nano /etc/nixos/configuration.nix
```

This file may look complex right now, but you only need to bother yourself with the
packages section for now. Press `Ctrl+F` to search for `environment.systemPackages`
and then press enter.

Here, you need to enter the name of the package you wish to install, say `neovim`. Once
you have done that, it should look something like this:

```nix
environment.systemPackages = with pkgs; [
    neovim
];
```

Save this to the file using `Ctrl+x`. When prompted for the file name, press enter to
keep the same name.

You can now install your package using this command:

```bash
sudo nixos-rebuild switch
```

This command will build a new version of your system and install. You don't even need
to reboot!

When you do reboot, you will see a new version of your system in the boot menu. By
default, the latest build that you switch to is the one that is used to boot up.

## Configuring the system

With NixOS, you can configure your system extensively by enabling or disabling services,
hardware and programs. This means everything from boot configuration to network
management is possible declaratively. You just need to know where to look.

The best advice I can give you is to open up the man page for NixOS Configuration
by using `man configuration.nix` in your terminal and search for whatever you can
think of editing.

Enabling services, hardware and more is as easy as below:

```nix
services.<service-name>.enable = true;
hardware.<hardware-name>.enable = true;
```

where `<service-name>` and `<hardware-name>` are the name of the service and hardware
you want to enable respectively.

There might be more options you might want to configure, in which case you need to
pass an attribute set to the element you want to configure. For example, if you want
to configure your firewall,

```nix
networking.firewall = {
    enable = true;
    allowedTCPPorts = {};
    allowedUDPPorts = {};
}
```

To see what attributes you can configure, you need to look up the man pages, same as
to look for what to enable.

One more thing you want to note is that apart from system packages, there are also
user packages which can only be accessed by a specific user. In the base configuration,
it looks something like this:

```nix
users.users.raikan = {
    isNormalUser = true;
    description = "raikan";
    extraGroups = [ "networkmanager" "wheel" ];
    packages = with pkgs; [
    ];
    shell = pkgs.zsh;
};
```

So you have the ability to set both system-wide and user-only installations and
configure them how the user sees fit!

## End Remarks

You now have enough knowledge to get started on tinkering with NixOS. I highly
encourage you to use NixOS and tweak the system as much as you like to get one that
is suited perfectly to you. If you would like to learn more about Nix and NixOS, do
go through the listed learning resources below.

1. [Nix Language Basics](https://nix.dev/tutorials/nix-language)
2. [A Tour of Nix](https://nixcloud.io/tour/?id=introduction/nix)
3. [Nix Language Reference](https://nix.dev/manual/nix/2.18/language/)
4. [Noogle](https://noogle.dev/)
