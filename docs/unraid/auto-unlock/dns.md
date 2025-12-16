---
title: DNS TXT Record Piece Storage
description: Storing auto unlock pieces in DNS TXT records for retrieval during boot.
---

# DNS TXT Record Piece Storage

DNS TXT records provide a simple and reliable method for storing auto unlock pieces.

## Overview

You create DNS TXT records containing your base64-encoded pieces. During boot, automatic disk unlock queries these records to retrieve the pieces needed to unlock your array.

!!! warning "DNS Privacy"
    DNS queries and responses are typically unencrypted and can be observed by network operators. Consider the following:

    - **Public DNS**: Avoid using public DNS for pieces containing sensitive data unless you're comfortable with potential exposure
    - **Local DNS**: Using a local DNS server (within your network) provides better privacy and control (can't be accessed from outside)

## Configuration Format

```text
dns:autounlock.example.com
```

Replace `autounlock.example.com` with your chosen domain name.

## Setup Instructions

### Step 1: Choose a Domain Name

Select a domain name for each piece. You can use:

- A subdomain of a domain you control (e.g., `share1.example.com`)
- A local domain served by your network's DNS server (e.g., `autounlock.local`)

### Step 2: Create DNS TXT Records

The method varies depending on your DNS provider or server:

#### OPNsense (Unbound DNS)

1. Navigate to **Services** → **Unbound DNS** → **Overrides**
2. Click the **+** button to add a new host override
3. Configure the override:
    - **Host**: Enter the subdomain (e.g., `autounlock`)
    - **Domain**: Enter the domain (e.g., `example.com` or `local`)
    - **Type**: Select **TXT**
    - **Value**: Paste the base64-encoded piece

4. Click **Save** and then **Apply**

#### Pi-hole (Local DNS)

1. SSH into your Pi-hole server or access the console
2. Edit the custom DNS configuration:

    ```bash
    sudo nano /etc/dnsmasq.d/05-autounlock.conf
    ```

3. Add a TXT record for each share:

    ```text
    txt-record=autounlock.local,"SHARE-PIECE"
    ```

4. Save the file and restart DNS:

    ```bash
    sudo systemctl restart dnsmasq
    ```

#### Adguard Home

1. Log in to the Adguard Home web interface
2. Navigate to **Filters** -> **Custom filtering rules**
3. Add a new rule, replacing `autounlock.local` with your chosen domain and `PIECE-DATA` with your base64-encoded piece:

    ```text
    ||autounlock.local^$dnsrewrite=NOERROR;TXT;PIECE-DATA
    ```

4. Save the changes

#### Unifi

Follow the instructions from the [Ubiquiti Help Center](https://help.ui.com/hc/en-us/articles/15179064940439-UniFi-DNS-Records-and-Local-Hostnames). Add a TXT record with the desired domain name and piece value.

#### Cloud DNS Providers

For providers like Cloudflare, AWS Route 53, or Google Cloud DNS:

1. Log in to your DNS provider's console
2. Navigate to your domain's DNS settings
3. Create a new **TXT record**:
    - **Name**: `autounlock` (or your chosen subdomain)
    - **Type**: `TXT`
    - **Value**: Paste the base64-encoded piece
    - **TTL**: 300 (or your preference)
4. Save the record

!!! note
    Some DNS providers require TXT record values to be enclosed in quotes.

### Step 3: Verify the DNS Record

Test that the DNS record is accessible:

```bash
dig TXT autounlock.example.com
```

You should see your piece value in the response.

### Step 4: Prepare Location String

```text
dns:autounlock.example.com
```

### Step 5: Add to Automatic Disk Unlock Configuration

--8<-- "include/auto-unlock/add-entry.snip"
