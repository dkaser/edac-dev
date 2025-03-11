---
title: Booting Unraid from XFS/BTRFS
description: Configuring a flash drive with a more resilient filesystem.
---

# Booting Unraid from XFS/BTRFS

--8<-- "include/warning.snip"

!!! note
    This process only works for EFI boot.

## Required Files

- [GRUB files](assets/unraid-grub-fs.tar.gz){: download="unraid-grub-fs.tar.gz"}

## Instructions

1. Create backup of flash drive, or install second flash drive if running instructions on Unraid. Determine device path of target flash drive.
2. Partition flash drive:
   ```
   sgdisk -Z /dev/sdX
   sgdisk -n 0:0:+512MiB -t 0:ef00 -c 0:efi /dev/sdX
   sgdisk -n 0:0:0 -t 0:8300 -c 0:UNRAID /dev/sdX
   ```

3. Create filesystems:
    ```
    mkfs.vfat /dev/sdX1

    mkfs.xfs -L UNRAID /dev/sdX2
    -or-
    mkfs.btrfs -L UNRAID /dev/sdX2
    ```

4. Mount filesystems:
   ```
   mkdir -p /mnt/unraid-flash/efi
   mkdir -p /mnt/unraid-flash/data

   mount /dev/sdX1 /mnt/unraid-flash/efi
   mount /dev/sdX2 /mnt/unraid-flash/data
   ```

5. Copy backup of flash drive to `/mnt/unraid-flash/data`
6. Extract GRUB files to `/mnt/unraid-flash/efi`. This should result in a `/mnt/unraid-flash/efi/EFI` and `/mnt/unraid-flash/efi/grub` folder.
7. Unmount flash drive:
   ```
   umount /mnt/unraid-flash/efi
   umount /mnt/unraid-flash/data
   ```