---
title: SMB Piece Storage
description: Storing Automatic Disk Unlock pieces on SMB/CIFS network shares for retrieval during boot.
---

# SMB Piece Storage

SMB (Server Message Block), also known as CIFS, is a common network file sharing protocol. You can store Automatic Disk Unlock pieces on SMB network shares hosted by Windows servers, NAS devices, or other file servers.

## Overview

You upload your base64-encoded piece to an SMB network share. During boot, Automatic Disk Unlock connects to the SMB share using credentials and retrieves the piece file.

## Configuration Format

```text
:smb,host=192.168.1.100,user=username,pass='OBSCURED_PASSWORD':share/path/to/share.txt
```

- `host`: Hostname or IP address of the SMB server
- `user`: Username for SMB authentication
- `pass`: Obscured password for the SMB user
- `share/path/to/piece.txt`: Share name and path to the piece file

## Setup Instructions

### Step 1: Obscure the Password

Automatic Disk Unlock requires the SMB password to be obscured before adding it to the configuration.

--8<-- "include/auto-unlock/obscure.snip"

### Step 2: Prepare location string

```text
:smb,host=192.168.1.100,user=username,pass='OBSCURED_VALUE':share/autounlock/piece.txt
```

Replace:

- `192.168.1.100` with your SMB server address
- `username` with your SMB username
- `OBSCURED_VALUE` with the obscured password from Step 1
- `share/autounlock/piece.txt` with your share name and path

### Step 3: Add to Automatic Disk Unlock Configuration

--8<-- "include/auto-unlock/add-entry.snip"
