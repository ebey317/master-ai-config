# Sensei Clean Connector Map

Plain version:

Sensei Clean should handle more than "files." It needs to understand where normal people actually keep stuff.

## Photos

Photos can come from:

- Pictures folder on the computer
- Camera Uploads folder
- Phone folders like `DCIM/Camera`
- USB drives and SD cards
- Google Photos
- iCloud Photos
- Amazon Photos
- OneDrive camera roll
- Dropbox camera uploads

Rule:

Sensei scans first. It shows the photos it found. It does not delete photos. If it wants to move extras, they go to a safe quarantine folder first.

## Cloud Drives

Cloud drives are file folders online:

- Google Drive
- OneDrive
- Dropbox
- Box
- pCloud
- Nextcloud / WebDAV

On this Linux computer, the best connector path is `rclone`.

Current live connection:

- Google Drive: `gdrive:`

Needed next:

- OneDrive through `rclone`
- Google Photos through `rclone` if the user wants photo-library cleanup
- Dropbox/Box later if the user uses them

## Email

Email is not the same as cloud drive.

Email needs separate connectors:

- Gmail
- Outlook / Hotmail / Microsoft 365 Mail
- Yahoo / IMAP

First version should scan only:

- big attachments
- duplicate attachments
- old newsletters
- old receipts

No email deleting in the first pass.

## Operating Systems

The app should feel the same on:

- Linux
- Windows
- macOS

The folder names change, but the idea is the same:

- Downloads
- Desktop
- Documents
- Pictures / Photos
- Videos
- Music
- Cloud sync folders
- Phone / USB / SD card mounts

## What the screen should say

Use words like:

- "Photos on this computer"
- "Phone photos"
- "Google Drive"
- "OneDrive"
- "Google Photos"
- "Email attachments"
- "Scan"
- "Review"
- "Move extra copy to Safe Quarantine"
- "Undo"

Avoid words like:

- adapter
- rclone
- monitored lane
- action
- apply
- provider root

