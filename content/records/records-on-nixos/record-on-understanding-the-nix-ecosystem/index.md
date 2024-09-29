---
draft: true

title: 'Advanced NixOS Customization with Nix Flakes and Home Manager'
subtitle: ''
summary: 'This is a deepdive into the Nix Ecosystem to understand how it works.'
description: 'Understanding the Nix store, Nix flakes and home manager'

date: 2024-09-27T23:08:18-07:00
lastMod: 2024-09-27T23:08:18-07:00

author: "raikan-san"
authorLink: "https://raikan-san.is-a.dev"
license: "<a rel='license external nofollow noopener noreffer' href='https://opensource.org/licenses/GPL-3.0' target='_blank'>GPL-3.0</a>"

tags: ["nix", "nixos"]
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
enableLastMod: true
enableWordCount: true
enableReadingTime: true
---

# Introduction

In the previous article, we saw how we can do basic configuration in NixOS. In this
article, we will accomplish two things:
1. Take a step back to explore understand how the Nix Ecosystem works so
when we actually configure our system, we know what is happening under the hood.
2. Learn why we use flakes and home-manager, and how they can be used to customize your 
NixOS configuration.

Let's get started!

## The Nix Package Manager

The Nix package manager is a purely functional package manager. In a functional language,
values are immutable. Nix treats packages as values, which means that once built, these
packages cannot be changed.

The unique way in which Nix stores the packages gives it the following advantages and
more:

1. Multiple versions
    
    Each program is stored separately along with its dependencies, meaning that there
    can be multiple versions of the same program without conflicting installations.

2. Atomic upgrades

    Upgrading a program happens atomically, meaning that a program will only have a
    single version at any given time. There is no overwriting like in imperative
    package managers.

3. Rollbacks

    As long as you have not cleaned up the previous generations, you can always roll back
    to an old build.

But how exactly is this possible?

### The Nix Store

A typical Linux operating system is FHS
([Filesystem Hierarchy Standard](https://en.wikipedia.org/wiki/Filesystem_Hierarchy_Standard))
compliant. This means that all the binaries are stored in `/bin` and all the libraries
that are essential for the binaries are stored in `/lib`. Let's see how they look in
NixOS.

```plaintext {title="Contents of /bin in my NixOS system"}
/bin
└── sh@ --> /nix/store/qqz0gj9iaidabp7g34r2fb...-bash-interactive-5.2p32/bin/bash
```

```plaintext {title="Contents of /lib in my NixOS system"}
/lib
└── ld-linux.so.2@ --> /nix/store/lbj3wv31dnvhx2xdhbkpcy...-stub-ld-i686-unknown-linux-musl
```

What we can see here is that these are symlinks
([symbolic links](https://en.wikipedia.org/wiki/Symbolic_link))
to a file or folder in the `/nix/store` directory.

The `/nix/store/` directory is referred to as the Nix store and is where all the packages
are stored. Nix is not FHS compliant. If we have a look at what the Nix store contains,
we will see, depending on how many packages you have installed, a long list of files and
folders with weird names.

The name is split into a few parts like so:
1. The hash

    This hash is created using all the necessary details for building the package from
    source. Everything from the sources, dependencies, compiler flags, and so on are used
    to create the hash.

    The hash itself is a SHA-256 hash which is then truncated to 160 bits, and further
    encoded in a base-32 notation.

    Using this hash, the Nix package manager ensures that there is no clash between
    multiple versions of the same package.

2. The package name

    In Nix, everything from a user profile to a software package is a package. This is
    what follows the hash to make sure that there is no conflict between different
    packages.

3. The package version

    This is the package version for the package being installed.

Say we have firefox installed on our system. Installing it creates a folder in the Nix
store that has all the necessary binaries, libraries and such in that folder with the
name `<hash>-firefox-<version>`. In my system, the firefox folder contains the following:

```plaintext {title="Firefox installation in my Nix store"}
/nix/store/8wcwfpcmd2r3bd2d4jm6w44594gjx5wc-firefox-130.0
├── bin
├── share
└── lib
```

We can see that everything that firefox needs to be able to run on the system is contained
in the store. If we have another version of firefox installed, there would be a folder
that contains everything for that firefox version to run on the system.

We can actually run firefox from the terminal by directly executing the binary like so:

```bash
/nix/store/8wcwfpcmd2r3bd2d4jm6w44594gjx5wc-firefox-130.0/bin/firefox
```

Doing this every single time to run firefox would be a pain. And this is considering we
have a single user. What if there is another user? How do they access firefox? What if
we have another user that wants a separate version of firefox to work with? How does Nix
differentiate between that?

### User Profiles in Nix

Every user in NixOS has a user profile that is stored in the `/home/$username/.nix-profile/`
directory where `$username` is your username. We can investigate the `~/.nix-profile/`
using the following command.

```bash
ls -l ~/.nix-profile/
```

This is the output (a bit modified to help understand easily).

```plaintext {title="Contents of my ~/.nix-profile"}
~/.nix-profile/
├── bin@          --> /nix/store/z16q0j81dxchr2zfa4x58y...-home-manager-path/bin
├── etc@          --> /nix/store/z16q0j81dxchr2zfa4x58y...-home-manager-path/etc
├── include@      --> /nix/store/z16q0j81dxchr2zfa4x58y...-home-manager-path/include
├── lib@          --> /nix/store/z16q0j81dxchr2zfa4x58y...-home-manager-path/lib
├── libexec@      --> /nix/store/z16q0j81dxchr2zfa4x58y...-home-manager-path/libexec
├── manifest.nix@ --> /nix/store/8ba7wkd1q3lf4xgykjslyv...-env-manifest.nix
├── opt@          --> /nix/store/z3zz5i4pcl6bb5gzlkhvh0...-discord-0.0.67/opt
├── rplugin.vim@  --> /nix/store/6zcpilxl3d2nd9z6w92bq4...-neovim-0.10.1/rplugin.vim
├── sbin@         --> /nix/store/z16q0j81dxchr2zfa4x58y...-home-manager-path/sbin
└── share@        --> /nix/store/z16q0j81dxchr2zfa4x58y...-home-manager-path/share
```

These are symlinks to the Nix store where different packages are stored. Because I am
using home-manager to manage my user profile (something we will get to in a bit),
I have symlinks to the latest build of home-manager which in turn contains all the folders
that the system needs like `/bin`, `/lib`, `/opt` and so on.

Let's take `/bin` in the nix-profile as an example. The content of every package that has
a `/bin` in it's build in the Nix store will be symlinked to the `/bin` in the
current build of home-manager which is then symlinked eventually to the `~/.nix-profile`.

If we take a step back and investigate further, we can find that `~/.nix-profile/` is
itself a symlink. Let us confirm this with the handy readlink tool.

```bash
$ readlink ~/.nix-profile
> /home/raikan/.local/state/nix/profiles/profile
```

Let's follow the link further.

```bash
$ readlink /home/raikan/.local/state/nix/profiles/profile
> profile-10-link
```

We see here that the symlink is also a symlink to another symlink called `profile-10-link`.
It might be different in your case, but this final symlink, is a symlink to what is called
a *user environment*.

We can see this if we do the following:

```bash
$ ls -l /home/raikan/.local/state/nix/profiles/profile*
> lrwxrwxrwx ... /home/raikan/.local/state/nix/profiles/profile -> profile-10-link
> lrwxrwxrwx ... /home/raikan/.local/state/nix/profiles/profile-10-link -> /nix/store/y1kd48az840d123lv5lllfk7mgc8ndb1-user-environment
> lrwxrwxrwx ... /home/raikan/.local/state/nix/profiles/profile-9-link -> /nix/store/9yp3731lgf0cn2k2ddbx9kbqc3h9wjsb-user-environment
```

The `profile-10-link` symlink refers to a user environment that contains what we saw in
the output of `ls -l ~/.nix-profile`.

The 10 here in the name refers to the generation number. We can confirm that it is indeed
the 10th generation by executing the following command:

```bash
$ nix-env --list-generations
>  9   2024-09-28 22:15:40
> 10   2024-09-29 00:23:46   (current)
```

We see that I have my current generation as 10, which is the same as the generation
number we saw before. If we were to switch generations to generation 9, the symlink
`/home/raikan/.local/state/nix/profiles/profile` would point to `profile-9-link`, and in
turn to another user environment instead.

User environments are created by Nix itself, and are packages too, which is why they
reside in the Nix store.

If you were to add another user in your NixOS configuration, a new user profile would be
created for them, and depending on what packages the new user has, the user environment
would be created or linked.

{{< admonition type=note open=false title="Cleaning up" >}}
Having many generations can take up a lot of space in your system. Nix does not remove
uninstalled packages by default. It also keeps previous versions of the packages that
have been installed. This is because you can roll back to a previous generation in NixOS.
So until you explicitly tell the package manager to get rid of the old generations, it
won't do so.

Removing the old generations is as easy as running `nix-env --delete-generations old`.
This removes all old (non-current) generations of your current profile. After getting
rid of the unused generations, you can run `nix-env --gc` to collect the garbage.

For more information on deleting generations, you can check out the man page for it by
executing `nix-env --delete-generations --help`.

An alternative is to use the `nix-collect-garbage` utility with the `-d` flag.
{{< /admonition >}}

## Nix Flakes

Nix Flakes is an experimental feature of the Nix package manager.

### Why Nix Flakes?

### Using Flakes

## Home Manager

### Why Home Manager?

### Using Home Manager
