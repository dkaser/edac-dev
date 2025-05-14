---
title: Booting Unraid from GRUB 2
description: Configuring a flash drive with GRUB2 instead of syslinux
---

# Booting Unraid from GRUB 2

--8<-- "include/warning.snip"

!!! note
    This process only works for EFI boot.

## Installation

### Quick Installation

Run the following command from the Unraid CLI:

```
curl -fsSL https://raw.githubusercontent.com/dkaser/unraid-grub/refs/heads/main/install.sh | sh
```

### Manual Instructions

1. If present, rename the `EFI` folder on the flash drive to `EFI-`.
2. Download GRUB to the EFI folder on the flash drive:
   ```
   mkdir -p /boot/EFI/boot/
   wget -O /boot/EFI/boot/BOOTX64.EFI https://github.com/dkaser/unraid-grub/releases/latest/download/BOOTX64.EFI
   ```
