---
title: Quick Start Guide
layout: rancher-default-v1.6
version: v1.6
lang: en
redirect_from:
  - /rancher/quick-start-guide/
---

## Quick Start Guide
---

In this guide, you'll learn how to quickly get started with Rancher v1.6, including:

* Preparing a Linux Host
* Launching Rancher Server (and Accessing the Rancher UI)
* Adding a Host
* Adding a Container through the Rancher UI

### Preparing a Linux Host

To begin, you'll need to install a [supported version of Docker]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/hosts/#supported-docker-versions) on a single Linux host. You can use your laptop, a virtual machine, or a physical server.

**Note:** Currently, Docker for Windows and Docker for Mac are not supported.

#### To Prepare a Linux Host:

1. Prepare a Linux host with 64-bit Ubuntu 16.04, at least **1GB** of memory, and a kernel of 3.10+.
2. Install a [supported version of Docker]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/hosts/#supported-docker-versions) on the host. To install Docker on the server, follow the instructions from [Docker](https://docs.docker.com/engine/installation/linux/ubuntulinux/).

### Launching Rancher Server

It only takes one command and a few minutes to install and launch Rancher Server. Once installed, you can open a web browser to access the Rancher UI.

Rancher uses GitHub to manage accounts and teams. We require read-only access to your basic GitHub account information.

> **Note:** We recommend configuring [access control]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/configuration/access-control/). Otherwise, your UI and API is available to anyone can access your IP address.

#### To Launch Rancher Server:

1. Run this Docker command on your host:
```bash
$ sudo docker run -d --restart=unless-stopped -p 8080:8080 rancher/server:stable
# Tail the logs to show Rancher
$ sudo docker logs -f <CONTAINER_ID>
```
After launching the container, we'll tail the logs of the container to see when the server is up and running. This process might take several minutes to complete.

2. To access the Rancher UI, go to `http://<SERVER_IP>:8080`, replacing `<SERVER_IP>` with the IP address of your host. The UI displays a Welcome page.

 > **Note:** If you are running your browser on the same host running Rancher server, you will need to use the host’s real IP address, such as `http://192.168.1.100:8080,` and not `http://localhost:8080` or `http://127.0.0.1:8080.`

3. Click **Authenticate with GitHub**. The Rancher UI displays.

>**Note:** Initially, Rancher creates a **Default** Cattle [environment]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/environments/) for you. Rancher supports grouping resources into multiple environments. Each one gets its own set of services and infrastructure resources, and is owned by one or more users or groups.

### Adding a Host

You can either add a host from a cloud provider that Rancher v1.6 supports, or you can add a custom host. If you don't see your cloud provider in the UI, don't worry. Simply use the custom host option.

The `Custom` option is selected by default. It provides the Docker command to launch the Rancher agent container. Rancher uses Docker Machine to launch hosts for the other cloud providers.

If you're adding a custom host, note these requirements:
* Typically, Rancher automatically detects the IP address to register the host.
  * If the host is behind a NAT or the same machine that is running the `rancher/server` container, you might need to explicitly specify its IP address.
* The host agent initiates a connection to the server, so make sure firewalls or security groups allow it to reach the URL in the command.
* All hosts in the environment must to allow traffic between each other for cross-host networking.
  * IPSec: `500/udp` and `4500/udp`
  * VXLAN: `4789/udp`

#### To Add a Host from a Cloud Provider:

1. From the **Infrastructure** menu, select **Hosts**. The Hosts page displays.
2. Click **Add Host**.
3. Enter your **Host Registration URL**, and click **Save**. This URL is where Rancher Server is running, and it must be reachable from any hosts you add. Ignore any warnings for now. The Add Host page displays.
4. Select your cloud provider:
   * Amazon EC2
   * Microsoft Azure
   * DigitalOcean
   * Packet
   * Other

5. Follow the instructions in the Rancher UI to add your host. This process might take a few minutes to complete. Once your host is ready, you can view its status on the Hosts page.

#### To Add a Custom Host:

1. From the **Infrastructure** menu, select **Hosts**. The Hosts page displays.
2. Click **Add Host**.
3. Enter your **Host Registration URL**, and click **Save**. This URL is where Rancher Server is running, and it must be reachable from any hosts you add. Ignore any warnings for now. The Add Host page displays.
4. Select **Custom**. A Docker command displays. For example:
   ```bash
   sudo docker run --rm --privileged -v /var/run/docker.sock:/var/run/docker.sock -v /var/lib/rancher:/var/lib/rancher rancher/agent:v2.0-alpha2
   http://<SERVER_IP>:8080/v3/scripts/D5433C26EC51325F9D98:1483142400000:KvILQKwz1N2MpOkOiIvGYKKGdE
   ```

   >**Note:** The IP address in the command must match your `<SERVER_IP>` and must be reachable from inside your host.

5. Copy, paste, and run the Docker command on your host to register it with Rancher. This process might take a few minutes to complete.
6. Click **Close**. On the Hosts page, you can view the status of your host.

After you add at least one host to your environment, it might take several minutes for all Rancher system services to launch. To verify the status of your environment, from the **Environment** menu, select **Manage Environments**. If a service is healthy, its state displays in green.

### Adding a Container through the UI

Once you've verified that all system services are up and running, you're ready to create your first container. To do so, create a stack and then add services to it. A `stack` is a convenient way to group services together.

#### To Add a Container through the UI:

1. On the Rancher UI menu, click **Stacks**. (Or, from the Welcome screen, click **Define a Service**.)
2. Click **Add Stack**. The Add Stack page displays.
3. Enter a **Name**, such as `first-container.`
4. Click **Create**. The new stack displays on the Stacks page.
5. Click **Add Service** in the new stack, and then enter a name, such as `first-service.` Rancher launches the container on the host. This process might take a few minutes to complete. You can view the status of the container on the Stacks page.

Regardless of your host's IP address, `first-container` will have an IP address in the `10.42.*.*` range, as Rancher has created a managed overlay network with the `ipsec` infrastructure service. This managed overlay network is how containers can communicate with each other across different hosts.

From the `first-container` drop-down list, you can perform management actions, such as stopping the container, viewing the logs, and accessing the container console.

Now that you've added hosts and your first container is up and running, you can check out the rest of the features in Rancher v1.6.

### Next Steps

[Configuring Access Control]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/configuration/access-control/)

About Rancher Server (tag info)

Other ways to create containers - new articles

Adding Services?
