---
title: Tailscale Options for Docker Containers
description: Explains different methods for connecting Docker containers to Tailscale, including TSDProxy with Label Manager, sidecar containers, LinuxServer.io's Tailscale Docker Mod, and "Use Tailscale".
---

# Tailscale Options for Docker Containers

!!! info
    Docker containers running on bridge networks do not need to have Tailscale installed within the container if the
    Tailscale plugin is installed. Bridge containers will automatically work on the Unraid server's Tailscale IP.

    Installing Tailscale directly on a container is useful in other scenarios:

    - Containers running on ipvlan/macvlan networks (e.g. br0)
    - Sharing a specific container via Tailscale.

## Methods

Several options are available to connect a container directly to Tailscale.

This is my order of preference:

1. Using TSDProxy and Label Manager
2. Using the Tailscale Docker Mod for LinuxServer.io containers
3. Running Tailscale as a sidecar container
4. "Use Tailscale" option in Docker settings

### Using TSDProxy and Label Manager

Advantages:

- Easy to set up
- One TSDProxy container can create Tailscale devices for many containers.
- Automatically generates valid TLS certificate for each service.

Disadvantages:

- Only works for HTTP(S) interfaces, other protocols cannot currently be proxied.
- Can be more complicated for containers on ipvlan/macvlan networks.

### Running Tailscale as a sidecar container

Advantages:

- Works with all ports/services provided by the container.

Disadvantages:

- More complicated to set up.
- One Tailscale device per sidecar container (multiple containers can be connected to one sidecar as long as their internal ports do not overlap).

### Using the Tailscale Docker Mod for LinuxServer.io containers

Advantages:

- Easy to set up
- Provides dedicated Tailscale device for the container

Disadvantages:

- Only works on LinuxServer.io containers.

### "Use Tailscale" option in Docker settings

Advantages:

- Built in to Unraid interface

Disadvantages:

- Modifies underlying container.
- Modifications can break the container, either when Tailscale is enabled or at any time in the future.
- Requires container authors to ignore security best practices.
