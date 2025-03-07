---
title: Advanced Tailscale Settings
description: How to configure a subnet router and set up an exit node using the Tailscale plugin for Unraid.
---

# Advanced Tailscale Settings

## Subnet Router

1. Open Tailscale settings.

    ![!settings-menu](assets/settings-menu.png)

2. Enter the desired route and click **Add**.

    ![!subnet-advertise](assets/subnet-advertise.png)

3. The routes will be displayed with a **Needs approval** message. The routes must be approved in the Tailscale
    admin console before they can be used.

    ![!subnet-pending](assets/subnet-pending.png)

4. Open the [Tailscale admin console](https://login.tailscale.com/admin/machines).
5. Locate the server in the machine list. It will have a blue **Subnets** badge with an exclamation point indicating
    that the server has routes that have not yet been approved.

    ![!subnet-approve](assets/subnet-approve.png)

6. Click the three dots at the end of the row, then **Edit route settings...**.

    ![!exit-menu](assets/exit-menu.png)

7. Check the routes to approve and save.

    ![!subnet-routes](assets/subnet-routes.png)

## Exit Node

1. Open Tailscale settings.

    ![!settings-menu](assets/settings-menu.png)
    
2. Click the **Enable** button next to **Run as Exit Node**.

    ![!exit-select](assets/exit-select.png)

3. The server will now advertise itself as an exit node. This must be approved in the Tailscale admin console before
    it can be used.

    ![!exit-pending](assets/exit-pending.png)

4. Open the [Tailscale admin console](https://login.tailscale.com/admin/machines).
5. Locate the server in the machine list. It will have a blue **Exit Node** badge with an exclamation point indicating
    that the server has not yet been approved as an exit node.

    ![!exit-approve](assets/exit-approve.png)

6. Click the three dots at the end of the row, then **Edit route settings...**.

    ![!exit-menu](assets/exit-menu.png)

7. Check **Use as exit node** and save.

    ![!exit-routes](assets/exit-routes.png)
