---
title: HTTP/HTTPS Piece Storage
description: Storing Automatic Disk Unlock pieces on web servers for retrieval during boot.
---

# HTTP/HTTPS Piece Storage

HTTP and HTTPS servers provide a flexible method for hosting Automatic Disk Unlock pieces. You can use any web server accessible during boot, including traditional web servers, cloud storage with HTTP access, or Tailscale Serve.

## Overview

You upload your base64-encoded piece as a text file to a web server. During boot, Automatic Disk Unlock makes an HTTP(S) GET request to retrieve the piece.

## Configuration Format

```text
:http,url='https://server.example.com/path/to/share.txt':
```

- Always use HTTPS when possible to encrypt the piece during transit
- The URL must point directly to the piece text file

## Setup Instructions

### Option 1: Traditional Web Server (e.g., Apache, Nginx)

1. On the web server, create a text file containing your base64-encoded piece:

    ```bash
    # The set commands below disable history logging so the piece isn't stored in shell history
    set +o history
    echo "SHARE-PIECE" > share.txt
    set -o history
    ```

2. Verify the file is accessible:

    ```bash
    curl https://server.example.com/path/to/share.txt
    ```

3. Add to Automatic Disk Unlock configuration:

    ```text
    :http,url='https://server.example.com/path/to/share.txt':
    ```

### Option 2: Tailscale Serve

[Tailscale Serve](https://tailscale.com/kb/1242/tailscale-serve) allows you to securely host files over your Tailnet without exposing them to the public internet. This is an excellent option for Automatic Disk Unlock pieces.

#### Prerequisites

- Tailscale installed on another device in your network (not the Unraid server that needs to be unlocked)
- That device must be powered on and connected to Tailscale during the Unraid server boot process
- On the Unraid server, either **Add Peers to /etc/hosts** must be enabled in Tailscale settings, or Tailscale DNS must be allowed and enabled.

    !!! note
        **Add Peers to /etc/hosts** is the preferred option, as enabling Tailscale DNS can cause problems in some configurations.

#### Setup Steps

1. On the device that will host the piece, create a directory for the piece files:

    ```bash
    mkdir -p /var/www/autounlock-pieces
    ```

2. Create a text file containing your base64-encoded piece:

    ```bash
    # The set commands below disable history logging so the piece isn't stored in shell history
    set +o history
    echo "SHARE-PIECE" > /var/www/autounlock-pieces/piece.txt
    set -o history
    ```

3. Start Tailscale Serve to host the file over HTTPS:

    ```bash
    tailscale serve --bg --https=8443 /var/www/autounlock-pieces
    ```

    !!! note
        The `--bg` flag runs Tailscale Serve in the background. The process will continue running after you close the terminal.

4. Verify the file is accessible from the Tailnet:

    ```bash
    curl https://hostname.tailnet-name.ts.net:8443/piece.txt
    ```

    Replace `hostname` with the Tailscale hostname of the device and `tailnet-name` with your Tailnet name.

5. Add to Automatic Disk Unlock configuration:

    ```text
    :http,url='https://hostname.tailnet-name.ts.net:8443/piece.txt':
    ```

!!! tip "Tailscale HTTPS Certificates"
    Tailscale automatically provisions HTTPS certificates for your devices. No additional certificate configuration is needed.

### Option 3: Cloud Storage with HTTP Access

Some cloud storage providers offer HTTP(S) access to files:

- **AWS S3** with pre-signed URLs or public access
- **Google Cloud Storage** with public URLs
- **Azure Blob Storage** with SAS tokens

Refer to your cloud provider's documentation for setting up HTTP access to files.

!!! warning
    Using public URLs can expose the piece to anyone with the URL.

## Adding to Automatic Disk Unlock Configuration

### Step 1: Prepare Location String

```text
:http,url='https://server.example.com/share.txt':
```

### Step 2: Add to Automatic Disk Unlock Configuration

--8<-- "include/auto-unlock/add-entry.snip"

## Security Considerations

!!! warning "Use HTTPS"
    Always use HTTPS instead of HTTP to encrypt the piece during transit. This prevents network observers from intercepting your pieces.

!!! tip "Access Control"

    - **Tailscale Serve**: Automatically restricts access to your Tailnet
    - **VPN**: Place the web server behind a VPN
    - **Firewall Rules**: Restrict access to specific IP addresses
    - **Basic Authentication**: You can use basic authentication by using a URL format like `https://user:pass@server.example.com/path/to/share.txt`
