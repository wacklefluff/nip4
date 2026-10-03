# nip4 --- a user interface for libvips

This is a reworking of [nip2](https://github.com/libvips/nip2) for the gtk4
UI toolkit and the vips8 image processing library.

There's a news post on libvips.org with an introduction:

https://www.libvips.org/2025/03/20/introduction-to-nip4.html

If you used nip2, there's another post outlining the main changes in the
new interface:

https://www.libvips.org/2025/03/12/nip4-for-nip2-users.html

# Introduction

nip4 is a spreadsheet-like interface to the [libvips image processing
library](https://libvips.github.io/libvips).  You create a set of formula
connecting your objects together, and on a change nip4 will recalculate.
Because nip4 uses libvips, it can process very large images, it recalculates
quickly, and it only needs a little memory. It scales to fairly complex
workflows: we have used it to develop systems with more than 10,000 cells,
analyzing images totalling many hundreds of gigabytes.

nip4 can load all workspaces from nip2, the previous version of this program.
It has a batch mode, so you can run any image processing system you develop
from the command-line and without a GUI. It is purely functional, meaning
there is no assignment and there are no side effects, and it is fully lazy,
meaning all computation is driven by the user interface, down to the pixel
level.

[![Screenshot](images/shot1.png)](images/shot1.png)

The CHARISMA workspace for museum technical imaging.

[![Screenshot](images/shot2.png)](images/shot2.png)

A 10,000 node workspace that computes per-voxel Patlaks from PET-CT scans.

# Installing

## Windows

Look in [releases](https://github.com/libvips/nip4/releases), pick a version, then download
(for example) `nip4-x64-9.1.5.zip` to get version 9.1.5 for x64 windows.

Unzip somewhere and run `bin/nip4.exe`.

By default, nip4 uses Vulkan to draw the UI, and this can be a bit wonky on
some older versions of Windows. If you see something like a blank window on
startup, try:

```console
> set GSK_RENDERER=cairo
> nip4
```

That will make it use a software only fallback to draw the UI and should
work on any version of Windows, though it will be a bit slower.

## macOS

You can install using [homebrew](https://brew.sh/) with:

```
brew install nip4
```

[ghostty has problems launching gtk
applications](https://github.com/ghostty-org/ghostty/discussions/3633),
so you might need to use Terminal.app, or set `XDG_DATA_DIRS` by hand.

## Linux-like systems with flatpak

Visit the nip4 page on flathub and click "Install":

https://flathub.org/en/apps/org.libvips.nip4

Alternatively, add flathub to your set of repositories:

```shell
flatpak remote-add --if-not-exists \
  flathub https://flathub.org/repo/flathub.flatpakrepo
```

Then install nip4 with:

```shell
flatpak install org.libvips.nip4
```

## From source

Download the sources, then build and install with eg.:

```
cd nip4-x.y.x
meson setup build --prefix /my/install/prefix
cd build
meson compile
meson test
meson install
```

Check the output of `meson setup` carefully.

## From source for Windows

Clone:

https://github.com/libvips/build-win64-mxe

Build with eg.:

```
$ ./build.sh --target x86_64-w64-mingw32.shared nip4
```

## Build from source for flathub

Add the `flathub` repo, if you don't already have it:

```shell
flatpak remote-add --if-not-exists \
  flathub https://flathub.org/repo/flathub.flatpakrepo
```

Install the gtk4 SDK and runtime:

```shell
flatpak install org.gnome.Sdk//51
flatpak install org.gnome.Platform//51
```

Allow file. Recent security changes to git will cause submodule checkout
to fail inside flatpak. If you get errors like `fatal: transport 'file'
not allowed`, re-enable file transport with:

```shell
git config --global protocol.file.allow always
```

You need the latest `flatpak-builder` (the one in deb is not new enough),
install with:

```
flatpak install org.flatpak.Builder
```

Build and try running it:

```shell
flatpak run org.flatpak.Builder --force-clean --user --install build-dir org.libvips.nip4.json
flatpak run org.libvips.nip4 ~/pics/k2.jpg
```

Force a complete redownload and rebuild (should only rarely be necessary) with:

```shell
rm -rf .flatpak-builder
```

Check the files that are in the flatpak you built with:

```shell
ls build-dir/files
```

Uninstall with:

```shell
flatpak uninstall nip4
```

## Notes on flatpak build process

- niftiio is annoying to build, skip it.

## Packaging for flathub

Install the appdata checker:

```shell
flatpak install flathub org.freedesktop.appstream-glib
flatpak run org.freedesktop.appstream-glib validate org.libvips.nip4.metainfo.xml
```

Also:

```shell
desktop-file-validate org.libvips.nip4.desktop
```

## Uploading to flathub

Make a PR on:

        https://github.com/flathub/org.libvips.nip4

then check the build status here:

        https://flathub.org/builds/#/apps/org.libvips.nip4

On success, merge to master.

