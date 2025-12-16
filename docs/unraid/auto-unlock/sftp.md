---
title: SFTP Piece Storage
description: Storing Automatic Disk Unlock pieces on SFTP servers for secure retrieval during boot.
---

# SFTP Piece Storage

SFTP (SSH File Transfer Protocol) provides secure, authenticated access to piece files. This method offers strong encryption and authentication, making it ideal for storing Automatic Disk Unlock pieces.

## Overview

You upload your base64-encoded piece to an SFTP server and configure Automatic Disk Unlock to retrieve it using SSH key authentication. During boot, Automatic Disk Unlock connects via SFTP and downloads the piece.

## Configuration Format

```text
:sftp,host=192.168.1.100,user=unraid,key_file=/boot/config/plugins/auto-unlock/id_ed25519,key_file_pass='OBSCURED_PASSWORD':/path/to/share.txt
```

- `host`: SFTP server hostname or IP address
- `user`: Username for SFTP authentication
- `key_file`: Path to the SSH private key
- `key_file_pass`: Obscured passphrase for the SSH private key (optional)
- `/path/to/share.txt`: Path to the share file on the SFTP server

## Setup Instructions

### Step 1: Generate an SSH Key Pair

On your Unraid server, generate a dedicated SSH key pair for Automatic Disk Unlock:

```bash
ssh-keygen -t ed25519 -f /boot/config/plugins/auto-unlock/id_ed25519 -C "auto-unlock"
```

When prompted:

1. **Enter passphrase**: Set a strong passphrase to protect the private key
2. **Enter same passphrase again**: Confirm the passphrase

!!! note
    The SSH key is stored on the Unraid flash drive at `/boot/config/plugins/auto-unlock/`. This ensures it persists across reboots.

### Step 2: Copy the Public Key to the SFTP Server

Display the public key:

```bash
cat /boot/config/plugins/auto-unlock/id_ed25519.pub
```

Copy the output and add it to the `~/.ssh/authorized_keys` file on the SFTP server for the user account that Automatic Disk Unlock will use:

```bash
# On the SFTP server

## Create a dedicated user (if needed) and switch to that user
adduser --disabled-password --gecos "" unraid
sudo -iu unraid

## Once running as the intended user, add the public key
mkdir -p ~/.ssh
chmod 700 ~/.ssh
echo "PUBLIC_KEY_CONTENT" >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

Replace `PUBLIC_KEY_CONTENT` with the actual public key from the Unraid server.

### Step 3: Upload the Piece File

Create a text file containing your base64-encoded piece on the download server:

```bash
# The set commands below disable history logging so the piece isn't stored in shell history
set +o history
echo "PIECE_STRING" > /path/to/unlock.txt
set -o history
```

### Step 4: Obscure the Key Passphrase

Automatic Disk Unlock needs the SSH key passphrase to use the key during boot. The passphrase must be obscured (encoded) before adding it to the configuration.

--8<-- "include/auto-unlock/obscure.snip"

### Step 5: Prepare Location String

```text
:sftp,host=192.168.1.100,user=unraid,key_file=/boot/config/plugins/auto-unlock/id_ed25519,key_file_pass='OBSCURED_VALUE':/path/to/piece.txt
```

Replace:

- `192.168.1.100` with your SFTP server address
- `unraid` with your SFTP username
- `OBSCURED_VALUE` with the obscured passphrase from Step 5
- `/path/to/piece.txt` with the path to your piece file

### Step 6: Add to Automatic Disk Unlock Configuration

--8<-- "include/auto-unlock/add-entry.snip"

## Advanced Configuration

### Using a Custom SSH Port

If your SFTP server uses a non-standard port:

```text
:sftp,host=192.168.1.100,port=2222,user=unraid,key_file=/boot/config/plugins/auto-unlock/id_ed25519,key_file_pass='OBSCURED_VALUE':/path/to/share.txt
```

### Using Password Authentication

While not recommended, you can use password authentication instead of SSH keys:

```text
:sftp,host=192.168.1.100,user=unraid,pass='OBSCURED_PASSWORD':/path/to/share.txt
```

Use the **Obscure Secret** feature to obscure the password before adding it to the configuration.

!!! warning
    SSH key authentication is more secure than password authentication. Use keys whenever possible.

## Security Considerations

!!! success "Strong Security"
    SFTP provides excellent security for Automatic Disk Unlock pieces:

    - **Encryption**: All data is encrypted during transit using SSH
    - **Authentication**: SSH keys provide strong authentication
    - **Access Control**: The SFTP server can restrict access by user and IP address

!!! tip "Best Practices"
    - Always use a passphrase to protect your SSH private key
    - Restrict SFTP user permissions to only the necessary directories
    - Consider using a dedicated SFTP user with limited privileges
