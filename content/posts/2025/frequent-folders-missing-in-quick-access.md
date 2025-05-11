---
title: Frequent Folders Missing in Quick Access
date: 2025-05-10T21:03:32-04:00
author: Lin
description: Fix for missing `Frequent folders` list in `Quick access`.
isStarred: false
toc: true
draft: false
---

## Introduction

In mid-April 2025, I encountered an issue with the `Quick access` page in File Explorer on Windows 10.
The `Frequent folders` list disappeared and the default view of the page changed to the `Details` view.
This was the second time I had encountered this issue — the first being in March 2024.
While this issue doesn't really affect my productivity, the change in appearance felt visually off and unfamiliar.

A quick Google search led me to [this post](https://superuser.com/questions/1569204/quick-access-frequent-folders-keeps-disappearing-on-windows-10-1909), which describes the issue and provides a fix.

## The Fix

Credits to [Keith Miller's](https://superuser.com/a/1569222) answer in the above-mentioned post.

### Root Cause

By default, Windows stores the settings for only 5000 folders.
Once that limit is reached, the issue occurs.

Here's a PowerShell command to check how many folders are currently stored:

```powershell
((gp "HKCU:\Software\Classes\Local Settings\Software\Microsoft\Windows\Shell\BagMRU").Nodeslots).count
```

### Resetting Folder Views

> [!IMPORTANT]
> Before modifying the registry, be sure to create a backup.

To reset the count and revert to the default appearance, two registry keys need to be deleted:

- `HKCU\Software\Classes\Local Settings\Software\Microsoft\Windows\Shell\BagMRU`
	- Stores the Most Recently Used (MRU) folder directory structures in its subkeys
- `HKCU\Software\Classes\Local Settings\Software\Microsoft\Windows\Shell\Bags`
	- Stores the folder view settings of the subkeys under BagMRU

Here's the command to manually delete the keys:

```cmd
reg delete "HKCU\Software\Classes\Local Settings\Software\Microsoft\Windows\Shell\BagMRU"
reg delete "HKCU\Software\Classes\Local Settings\Software\Microsoft\Windows\Shell\Bags"
```

This will give a confirmation prompt before deleting.
To bypass it, append the `/f` flag.

Below is the full batch script to delete the keys and restart File Explorer:

```cmd
@echo off
reg delete "HKCU\Software\Classes\Local Settings\Software\Microsoft\Windows\Shell\BagMRU" /f
reg delete "HKCU\Software\Classes\Local Settings\Software\Microsoft\Windows\Shell\Bags" /f
taskkill /im explorer.exe /f
start explorer.exe
exit
```
