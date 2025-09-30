# Rsync Backup System

A systemd-based backup storage solution using rsync with resource management and safety features.

## Overview

This backup system was originally created for personal server use and enhanced for more reliable operation. It uses systemd templates to manage multiple backup configurations with proper resource controls.

## Requirements

- systemd-based Linux distribution
- rsync installed
- User systemd support enabled
- Mount points for source and destination

## Features

- Resource-friendly execution (low CPU/IO priority)
- Prevents multiple backups from running simultaneously
- Mount point validation before backup starts
- Configurable per-backup settings
- Error notification by ntfy

## Files

- `rsync-backup@.service` - Main backup service template
- `rsync-backup@.timer` - Scheduler with randomized timing
- `*.env` - Configuration files for each backup job
- `notify-error@.service` - In case of an error, ntfy.sh will notify you

## Installation

```bash
# Copy service files
cp rsync-backup/rsync-backup@.service ~/.config/systemd/user/
cp rsync-backup/rsync-backup@.timer ~/.config/systemd/user/
cp rsync-backup/notify-error@.service ~/.config/systemd/user/

# Create config directory
mkdir -p ~/.config/rsync-backup

# Reload systemd
systemctl --user daemon-reload
```

## Configuration

Create an environment file for your backup:
```bash
# Copy and edit the example
cp config/example.env ~/.config/rsync-backup/storageA.env
```
Important: Both SOURCE and DESTINATION must be mount points (not subdirectories). The service validates mount points before backup execution and will fail if either path is not a mounted filesystem.

## Notification

Edit the ntfy topic in `notify-error@.service` to match your setup.

## Usage

```bash
# Enable daily backups
systemctl --user enable rsync-backup@documents.timer
systemctl --user start rsync-backup@documents.timer

# Run backup manually
systemctl --user start rsync-backup@documents.service

# Check status
systemctl --user status rsync-backup@documents.timer
```

## Safety Features

- Mount point validation: Verifies both source and destination are mounted filesystems using mountpoint command
- Uses file locking to prevent concurrent backups
- Runs with low system priority to avoid performance impact
- Automatic error notifications

## License

MIT License
