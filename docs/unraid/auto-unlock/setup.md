---
title: Automatic Disk Unlock Setup
description: Installing the Automatic Disk Unlock plugin and configuring automatic array unlock.
---

# Automatic Disk Unlock Setup

These instructions will help you install the Automatic Disk Unlock plugin and configure it to automatically unlock your encrypted Unraid array at boot time.

!!! warning
    Automatic Disk Unlock is designed for encrypted arrays. If you haven't already encrypted your array, do so before proceeding with this guide.

## Installation

1. Log in to the Unraid server and switch to the **Apps** tab.
2. Search for **Automatic Disk Unlock**.
3. Install **Automatic Disk Unlock**.
4. Click **Done** once the window shows that Automatic Disk Unlock has been installed.

## Generating Pieces

After installation, you need to generate the pieces that will protect your disk encryption key.

1. Switch to the **Settings** tab, then click on **Automatic Disk Unlock**.
2. In the **Initialize** section, configure the following:
    - **Number of Pieces to Create**: The total number of pieces to split the wrapping key into (e.g., 5)
    - **Number of Pieces Needed for Unlock**: The minimum number of pieces required to unlock the array (e.g., 3)

    !!! tip
        Choose values that balance security with reliability. If the system cannot access enough pieces during boot, the array will not unlock.

3. Enter your current disk encryption passphrase in the provided field, or add your key file.

4. Click **Initialize** to generate the pieces.

    !!! note
        The plugin will verify that the passphrase or key file can unlock your drives before generating pieces.

5. After initialization completes, the pieces will be displayed **one time only**. Each piece is a base64-encoded string.

    !!! warning
        **IMPORTANT**: Copy and securely save all pieces immediately. These will never be displayed again. If you lose access to the needed number of pieces, you will need to generate a new set.

6. Distribute the pieces to secure locations. You might store them in:
    - DNS TXT records
    - Remote web servers
    - SFTP servers
    - SMB network shares
    - S3-compatible storage buckets

## Configuring Auto-Unlock

After saving your pieces, the configuration screen will switch to configuration mode where you can specify where each piece is stored.

1. For each piece location, prepare a location string using the format specific to your storage method:

    - **[DNS](dns.md)**: `dns:autounlock.example.com`
    - **[HTTP/HTTPS](http.md)**: `:http,url='https://server.example.com/share.txt':`
    - **[SFTP](sftp.md)**: `:sftp,host=192.168.1.100,user=unraid,key_file=/boot/config/plugins/auto-unlock/id_ed25519:/path/to/share.txt`
    - **[SMB](smb.md)**: `:smb,host=192.168.1.100,user=username,pass=OBSCURED_PASSWORD:share/path/to/share.txt`
    - **[S3](s3.md)**: `:s3,provider=AWS,access_key_id=KEYID,secret_access_key=SECRET,region=us-east-1:bucket/share.txt`

    !!! note
        You need to configure at least the required number of locations. For example, if your threshold is 3, configure at least 3 piece locations.
        It is recommended to configure more than the minimum for redundancy.

    !!! info
        These locations are examples. Most [rclone backends](https://rclone.org/docs/#configure) should work for piece storage.
        Refer to the documentation for your chosen backend for additional configuration options. Some common options are shown in the individual storage method guides.

2. Test each location before adding it to the configuration:
    - Paste the location string into the **Test Location** input field
    - Click **Test Location** to verify the piece can be retrieved successfully
    - If the test fails, verify the location string and piece file are correct

3. After successfully testing a location:
    - Navigate back to the **Configuration** section
    - Add the location string to the **Piece Locations** text area (one per line)
    - Click **Save** to store your configuration

4. Click **Test Configuration** to perform an end-to-end test. This will:
    - Retrieve the configured number of pieces
    - Reconstruct the wrapping key
    - Decrypt the encrypted keyfile
    - Verify it can unlock your drives

    !!! success
        If the test passes, Automatic Disk Unlock is properly configured and will automatically unlock your array on the next boot.

## Disable Manual Array Start

Once Automatic Disk Unlock is configured and tested, you should disable the native automatic start option (if you were using another method to automatically start the array):

1. Navigate to **Settings** → **Disk Settings**
2. Disable **Enable auto start** (set to **No**)
3. Click **Apply**

This prevents Unraid from waiting for manual passphrase entry, since Automatic Disk Unlock will handle the unlocking process automatically during boot.

## Next Steps

- Learn [how Automatic Disk Unlock works](how.md) and the security model
- Set up specific piece storage methods:
  - [DNS TXT Records](dns.md)
  - [HTTP/HTTPS Servers](http.md) (including Tailscale Serve)
  - [SFTP Servers](sftp.md)
  - [SMB Shares](smb.md)
  - [S3-Compatible Storage](s3.md)
