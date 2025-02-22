---
description: ' How to add IP addresses for external devices (Android and iOS) to connect
  to the Asset Processor or allow remote console access to a running game instance. '
title: Adding IP Addresses to Allow Access to the Asset Processor and Remote Console
---

{{< preview-migrated >}}

The Asset Processor is a networked application that O3DE uses to build source assets into game engine ready assets. To ensure your external device can connect to the Asset Processor, you must add the IP address of the external device (Android or iOS) to the **allow\_list** in the `bootstrap.cfg` file.

The Universal Remote Console is a networked application that O3DE uses to send commands and view output from the running game engine. To ensure remote console access to a running game instance on your external device, you must add the IP address of the computer that will run the remote console to the **log\_RemoteConsoleAllowedAddresses** list in the appropriate configuration file for your project:

+ Android - `system_android_es3.cfg`
+ iOS - `system_ios_ios.cfg`

You must update the configuration file to include the allowed IP addresses before you deploy your game to the external device.
