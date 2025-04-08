---
title: Plugin Diagnostics
description: Installing the plugin and downloading diagnostics.
---

# Plugin Diagnostics: Usage 

## Installation

1. Log in to the Unraid server and switch to the **Apps** tab.
2. Search for and install **Plugin Diagnostics**.

    ![!install-plugin](assets/install-plugin.png)

4. Click **Done** once the window shows that Plugin Diagnostics has been installed.

    ![!install-complete](assets/install-complete.png)

## Downloading Diagnostics (GUI)

1. Switch to the **Tools** tab, then click on **Plugin Diagnostics**.

    ![!tools-menu.png](assets/tools-menu.png)

2. Click the button for the intended plugin.

    !!! note
        This only shows plugins that are configured to use Plugin Diagnostics.

    ![!tools-menu.png](assets/download-diags.png)

## Downloading Diagnostics (CLI)

Plugin diagnostics can be created via CLI by running `plugin-diagnostics pluginName`. The resulting diagnostics will be stored in the **logs** folder on the flash drive.