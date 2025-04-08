---
title: Plugin Diagnostics
description: Enabling plugin diagnostics for Unraid plugins
---

# Plugin Diagnostics: Developers 

## Overview

Any plugin can opt-in to Plugin Diagnostics by creating a `diagnostics.json` file. Plugin Diagnostics scans all folders in `/usr/local/emhttp/plugins` for a configuration file.

## Filters

Diagnostic packages should be anonymized as much as possible since they are frequently posted in public locations (e.g. forums or Github issues). IP addresses are automatically sanitized, and plugin authors should include additional filters as required. Filters must be sed-compatible, using extended regex syntax (`sed -r`).

## Schema

- [https://github.com/dkaser/unraid-plugin-diagnostics/blob/main/schema/diagnostics.json](https://github.com/dkaser/unraid-plugin-diagnostics/blob/main/schema/diagnostics.json)

## Syntax

`title`
:   **Type:** String  
    **Description:** Name to display in WebGUI  
    **Required:** True

`filters`
:   **Type:** Array of strings  
    **Description:** sed substitutions to be applied to all output  
    **Required:** False

`commands`
:   **Type:** Array of objects  
    **Description:** Commands to collect diagnostic data  
    **Required:** False

    **Object properties:**

      - `command`
        - **Type:** String
        - **Description:** Shell command to execute 
      - `file`
        - **Type:** String
        - **Description:** Filename for command output 
      - `filter`
        - **Type:** Array of strings
        - **Description:** sed substitutions to be applied to file 

`files`
:   **Type:** Array of strings and/or objects  
    **Description:** Files to collect  
    **Required:** False

    **Object properties:**

      - `file`
        - **Type:** String
        - **Description:** Filename to collect 
      - `filter`
        - **Type:** Array of strings
        - **Description:** sed substitutions to be applied to file 

`system_diagnostics`
:   **Type:** Array  
    **Description:** Include Unraid system diagnostics   
    **Required:** False  
    **Default:** False

## Example

```
{
    "title": "My Plugin",
    "filters": [
        "s/find/replace/g"
    ],
    "commands": [
        {
            "command": "lsblk",
            "file": "lsblk.txt"
        },
        {
            "command": "zfs list",
            "file": "zfs.txt",
            "filters": [
                "s/^(.)(\\S*)/\\1zzz/g"
            ]
        }
    ],
    "files": [
        "/etc/ssh/sshd_config",
        {
            "file": "/etc/samba/smb-shares.conf",
            "filters": [
                "s/\\[.*\\]/\\[Share\\]/g",
                "s/path = \\S*/path = path/g",
                "s/list = \\S*/list = list/g",
                "s/users = \\S*/users = users/g"
            ]
        }
    ],
    "system_diagnostics": true
}
```