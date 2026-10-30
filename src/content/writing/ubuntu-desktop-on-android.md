---
title: An Ubuntu desktop on Android with Termux
description: Running Ubuntu 26.04 with XFCE on a phone, using proot-distro and Termux:X11 — and the one flag that fixes "Cannot open display".
date: 2026-10-18
tags: ['linux', 'android']
---

Android is a Linux kernel with a userland full of surprises, but it can host a real desktop. This
is the setup I used to run Ubuntu 26.04 with XFCE on a phone, using Termux, `proot-distro`, and
Termux:X11.

No root, no custom ROM. The cost is performance — this is not a replacement for a laptop — but it
does work, and it is a genuinely useful way to test Linux software on a device you always carry.

## Install

Start with Termux from F-Droid (the Play Store build is out of date and lacks packages you
need):

```bash
pkg update && pkg upgrade -y
pkg install proot-distro
proot-distro install ubuntu
```

Log in and install the desktop:

```bash
proot-distro login ubuntu
apt update
apt install xfce4 xfce4-terminal dbus-x11 x11-apps -y
```

Then, back in Termux, the display server:

```bash
pkg install x11-repo -y
pkg install termux-x11-nightly -y
```

Install the **Termux:X11** APK as well — the package provides the tools, the app provides the
screen.

## The flag that matters

My first attempt died with:

```text
xfce4-session: Cannot open display
```

Two causes, and they compound:

1. Logging into Ubuntu without `--shared-tmp`, so the proot and Termux environments do not share
   `/tmp` — which is where the X socket lives.
2. `termux-x11` was not running when the session started.

So log in like this, and make an alias of it:

```bash
proot-distro login ubuntu --shared-tmp
```

```bash
alias ubuntu-desktop="proot-distro login ubuntu --shared-tmp"
```

## The routine

Each time you want a desktop:

```bash
termux-x11 :0 &
```

Open the Termux:X11 app. A black screen is normal at this point — it is a display waiting for a
client.

```bash
ubuntu-desktop
export DISPLAY=:0
dbus-launch --exit-with-session xfce4-session
```

XFCE appears in the Termux:X11 window.

## When it breaks

`xfce4-session: Cannot open display` almost always means one of the two causes above. Check that
the server is alive:

```bash
ps -ef | grep termux-x11
```

```text
termux-x11 com.termux.x11 :0
```

Then test the connection with the least demanding client there is:

```bash
xclock
```

A clock window means X11 is fine and the problem is in the desktop session, not the display.

Warnings about `xkbcomp` can be ignored. A black window in Termux:X11 before you start a session
is expected.

## What I would do next

Git, build tools, NVM and Node.js, `pnpm`, then `code-server` for editing — at which point a phone
with a Bluetooth keyboard is a workable thin client for whatever machine you are actually
coding on.
