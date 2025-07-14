# OSX on VMware ESXi Installation Guide

## Overview
This guide shows how to create a bootable macOS installer for VMware ESXi. Based on the original Big Sur guide but updated for modern macOS versions.

**Reference:** https://vmscrub.com/installing-macos-11-big-sur-on-esxi-7-update-1/

## Prerequisites
- VMware ESXi 7.0 or later
- macOS host system (for creating the installer)
- ESXi unlocker (to enable macOS support)
- Sufficient storage space (20GB+ recommended)

## Step 1: Download macOS Installer
Go to the Mac App Store and download the macOS installer. If you can't download the current version directly, you may need to download a previous version first.

**Note:** If there's a workaround for downloading the current version directly, please update this section.

## Step 2: Create Bootable Installer

### Create the disk image
```bash
hdiutil create -o /tmp/sequoia -size 17100m -volname sequoia -layout SPUD -fs HFS+J
```

**Important:** The size parameter may vary depending on the macOS version:
- **macOS Big Sur:** 12900m - 13100m
- **macOS Monterey:** 13500m - 14000m  
- **macOS Ventura:** 14000m - 15000m
- **macOS Sonoma:** 15000m - 16000m
- **macOS Sequoia:** 17000m - 17500m

*Adjust the size as needed if you encounter "not large enough" errors during the createinstallmedia step.*

### Mount the disk image
```bash
hdiutil attach /tmp/sequoia.dmg -noverify -mountpoint /Volumes/sequoia
```

### Create the install media
```bash
sudo /Applications/Install\ macOS\ Sequoia.app/Contents/Resources/createinstallmedia --volume /Volumes/sequoia --nointeraction
```

**Note:** Replace "Sequoia" with your macOS version name (e.g., "Big Sur", "Monterey", "Ventura", "Sonoma").

### Eject the disk
```bash
hdiutil eject -force /Volumes/Install\ macOS\ Sequoia
```

### Convert to CDR format
```bash
hdiutil convert /tmp/sequoia.dmg -format UDTO -o /Users/root/Desktop/Sequoia.cdr
```

### Rename CDR to ISO
```bash
mv /Users/root/Desktop/Sequoia.cdr /Users/root/Desktop/Sequoia.iso
```

## Step 3: Clean Up
Remove temporary files:
```bash
rm -rf /tmp/sequoia*
```

## Step 4: Upload to ESXi
Upload the resulting ISO file to your ESXi datastore and use it as the installation media for your macOS VM.

## Important Notes
- Ensure you have the ESXi unlocker installed before attempting to create a macOS VM
- macOS Sequoia requires AVX2 CPU instruction support
- Consider installing an older macOS version first, then upgrading to the current version if you encounter compatibility issues
- Always comply with Apple's Software License Agreement when running macOS

## Troubleshooting
- If you get "not large enough" errors, increase the size parameter in the hdiutil create command
- If the installer app name is different, adjust the path in the createinstallmedia command
- Ensure you have sufficient disk space for the conversion process

## Contributing
If you find improvements or workarounds, please submit a pull request or open an issue.
