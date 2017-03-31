---
title: Installing Wavefront Proxies
keywords:
tags: [proxies]
sidebar: doc_sidebar
permalink: proxies_installing.html
summary: This topic describes how to install Wavefront proxies.
---
Before metrics can begin streaming to Wavefront from a host or application you must add a Wavefront proxy to your installation. This article describes several methods for installing a Wavefront proxy: scripted installation and manual installation. Both methods set up a basic configuration. The scripted installation optionally allows you to install and configure a [Telegraf collector agent]().
 
## Requirements
Before installing a proxy, ensure that you have:

- A Wavefront server API URL. For example: https://\<your_instance\>.wavefront.com/api/.
- An account on the Wavefront system.
- A host machine that:
  - Has Internet access (specifically, the Wavefront server URL at port 443). Make sure that `curl <your_instance>` from this host results in some response (rather than timing out).
  - Has sufficient memory.  The host machine does not need to be dedicated to running the Wavefront proxy; the proxy does not use a large amount of CPU, memory, or storage. However, we  recommend running the proxy on a machine with at least 4GB of free memory.
  - Runs one of the following operating systems:
    - Ubuntu 12.04, 14.04, 16.04
    - CentOS 6.5, 7
    - RHEL 6, 7
    - Debian 7, 8, 9, 10
    - Amazon Linux

  If you do not see your operating system, contact [support@wavefront.com](mailto:support.wavefront.com).

## Scripted Installation on a Single Host
The Wavefront application has a wizard that guides you through installing a Wavefront proxy and optionally a Telegraf collector agent and setting up a basic configuration using scripts. To access the wizard you must have Proxy Management permission. To run the wizard:

1. Open the Wavefront application UI.
1. Select **Browse > Proxies**.
1. Select **Add > New Proxy** at the top of the filter bar. The Populate Your Data screen displays.
1. Under **WAVEFRONT PROXY**, click Add Now <i class="fa fa-arrow-right"></i> - Add a Wavefront proxy and optionally a Telegraf collector agent. A script displays that runs a Wavefront CLI command to install a Wavefront proxy on your host and register with Wavefront.
1. Copy the script and run on your host.
1. When the installation completes, click **Next**. The screen reports `Found and registered <hostname>`.
1. Click **Next**. Instructions for running a script to install the Telegraf collector agent display.
1. Optionally copy the script and run on your host.
1. Click **Next**, then **Done** twice. The Proxies page displays. Verify that your proxy is listed.

You can also install and run the Wavefront CLI directly. For more information, see [Wavefront CLI](wavefront_cli).

## Manual Package Installation on a Single Host
You can manually install a Wavefront proxy .rpm or .deb package available at [Wavefront proxy packages](https://packagecloud.io/wavefront/proxy). The installation packages include an interactive script for configuring the proxy. To install a proxy package and set up a basic configuration:

1. Run one of the scripts that set up the installation process.
1. Go to the Wavefront proxy directory created during the package installation: `cd /opt/wavefront/wavefront-proxy`
1. Run the interactive configuration script: `bin/autoconf-wavefront-proxy.sh`. The script prompts you for the following properties:
  - **server** - The Wavefront server API URL.
  - **token** - An API token. The token is a hexadecimal string of randomly generated alpha-numeric characters and dashes. For example: **f411c16b-3cf7-4e03-bf1a-8ca05aab899d**.  To get a token:
    1. Log into your Wavefront account.
    1. Click the gear icon  at the top right of the task bar and click your username.
    1. In the API Access box, click **Generate** to generate an API token.
    1. Copy the token.
  - **hostname** - A name (alphanumeric plus periods) unique across your entire account representing the machine that the proxy is running on. The hostname is not used to tag your data; rather, it's used to tag data internal to the proxy, such as JVM statistics, per-proxy point rates, and so on.
  - **enable graphite** - Indicate whether to enable the Graphite format. See [Sending Graphite Data to Wavefront]() for details on Graphite configuration.
When the interactive configuration is complete, the Wavefront proxy configuration at `/etc/wavefront/wavefront-proxy/wavefront.conf` is updated with the input that you provided and the wavefront-proxy service is started.

## Installing Proxies on Multiple Hosts
To install proxies on multiple hosts you can use Ansible. For details, see [Installing Proxies and Agents with Ansible]().

## Running a Proxy in a Docker Container
You can alternatively run a proxy in a Docker container. For details, see [Running a Wavefront Proxy in Docker]().

{% include links.html %}