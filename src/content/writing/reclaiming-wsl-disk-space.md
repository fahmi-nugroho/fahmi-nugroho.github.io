---
title: Reclaiming disk space from WSL2
description: Deleting files inside WSL does not shrink its virtual disk. Here is the six-step dance that actually returns gigabytes to Windows.
date: 2026-10-30
tags: ['linux', 'wsl']
---

WSL2 keeps its entire Linux filesystem inside a single virtual disk file, `ext4.vhdx`. That file
grows on demand — but it never shrinks on its own. If you install a few toolchains, build a
project, then delete the build output, drive C stays exactly as full as it was while
`df -h` inside WSL politely reports plenty of free space.

Freeing that space back to Windows takes a few steps, and they have to happen in this order.

## 1. Delete what you actually want gone

Nothing below can recover space that you have not deleted first. Clean your package caches and
whatever else has piled up:

```bash
sudo apt remove <package>
sudo apt autoremove
sudo apt clean
pip uninstall <package> -y
rm -rf /path/no/longer/needed
```

## 2. Trim the filesystem

```bash
sudo fstrim -v /
```

Sample output:

```text
/: 8.4 GiB (9023453184 bytes) trimmed
```

The number here is an upper bound on what you can get back — `fstrim` only marks blocks as
unused so the virtual disk knows they can be released. It does **not** shrink `ext4.vhdx` by
itself. The bigger the trimmed number, the more the next step can reclaim.

## 3. Shut WSL down

From an elevated PowerShell or Command Prompt:

```powershell
wsl --shutdown
```

Compaction cannot run while any distribution is holding the disk open. Close your terminals
first, then run this.

## 4. Find the disk file

```powershell
Get-ItemProperty Registry::HKCU\Software\Microsoft\Windows\CurrentVersion\Lxss\* |
  Select-Object DistributionName, BasePath
```

```text
DistributionName BasePath
---------------- --------
Ubuntu           C:\Users\<user>\AppData\Local\wsl\{guid}
```

The file you want is `BasePath\ext4.vhdx`.

## 5. Compact it

```powershell
diskpart
```

```text
select vdisk file="C:\Users\<user>\AppData\Local\wsl\{guid}\ext4.vhdx"
attach vdisk readonly
compact vdisk
detach vdisk
exit
```

`attach vdisk readonly` matters: it lets DiskPart operate on a disk that nothing else has
claimed, without risking a write. Let `compact vdisk` reach 100% before detaching.

## 6. Start WSL again

Open Ubuntu as usual. It picks up the compacted file, and drive C gets the space back.

## What actually does what

- `fstrim` tells the virtual disk which blocks are free. It marks, it does not shrink.
- `compact vdisk` is the only step that reduces the size of `ext4.vhdx`.
- Compaction is safe to run as long as you are compacting the right file and WSL is shut down.
  It does not touch data inside the filesystem.

The whole point of doing this in order is that each step enables the next: delete, so there is
something to trim; trim, so there is something to compact.
