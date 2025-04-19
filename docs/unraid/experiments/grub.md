---
title: Booting Unraid from GRUB 2
description: Configuring a flash drive with GRUB2 instead of syslinux
---

# Booting Unraid from GRUB 2

--8<-- "include/warning.snip"

!!! note
    This process only works for EFI boot.

## Easy Installation

### Required Files

- [GRUB files](assets/grub-boot.zip){: download="grub-boot.zip"}

### Instructions

1. If present, rename the `EFI` folder on the flash drive to `EFI-`.
2. Extract the contents of **grub-boot.zip** to the root of the flash drive.

!!! info
    This should result in an `EFI` and `grub` folder being present in the root of the flash drive.

## Installation using grub-install

1. Download the required configuration files:
    - Embedded configuration: [grub-load.cfg](assets/grub-load.cfg){: download="grub-load.cfg"}
    - Boot configuration: [grub.cfg](assets/grub.cfg){: download="grub.cfg"}

2. Download the latest GRUB 2 Slackware package (currently 2.12).
3. In Unraid, from the folder containing the Slackware package and the two config files, install GRUB to the flash drive:
   ```
   installpkg grub-2.12-x86_64-16.txz
   grub-install --removable --efi-directory=/boot --no-bootsector dummy
   grub-mkimage --directory '/usr/lib64/grub/x86_64-efi' --prefix '' --config 'grub-load.cfg' --output '/boot/grub/x86_64-efi/unraid.efi' --format 'x86_64-efi' --compression 'auto' 'fat' 'part_msdos'
   cp /boot/grub/x86_64-efi/unraid.efi /boot/EFI/boot/bootx64.efi
   cp grub.cfg /boot/grub/
   ```