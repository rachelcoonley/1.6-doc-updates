---
title: Quick Start Guide
layout: rancher-default-v1.6
version: v1.6
lang: en
---

## Quick Start Guide
---

In this guide, you'll learn how to quickly get started with Rancher v1.6, including:

* Preparing a Linux Host
* Launching Rancher Server (and Accessing the Rancher UI)
* Working with Your First Environment
* Adding Hosts
* Adding Containers
* Launching Catalog Applications

### Preparing a Linux Host

To begin, you'll need to install a [supported version of Docker]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/setup/#supported-docker-versions) on a single Linux host. You can use your laptop, a virtual machine, or a physical server.

>**Note:** Currently, Rancher does not support Docker for Windows and Docker for Mac.

#### To Prepare a Linux Host:

1. Prepare a Linux host with 64-bit Ubuntu 16.04, at least **1GB** of memory, and a kernel of 3.10+.
2. Install a [supported version of Docker]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/setup/#supported-docker-versions) on the host. To install Docker on the server, follow the instructions from [Docker](https://docs.docker.com/engine/installation/linux/ubuntulinux/).

### Launching Rancher Server

It only takes one command and a few minutes to install and launch Rancher Server. Once installed, you can open a web browser to access the Rancher UI.

> **Note:** We recommend configuring [access control](http://rancher.com/docs/rancher/v1.6/en/configuration/access-control/). Otherwise, your UI and API is available to anyone can access your IP address.

#### To Launch Rancher Server:

1. Run this Docker command on your host:
   ```bash
   $ sudo docker run -d --restart=unless-stopped -p 8080:8080 rancher/server:stable
   # Tail the logs to show Rancher
   $ sudo docker logs -f <CONTAINER_ID>
   ```
   After launching the container, we'll tail the logs of the container to see when the server is up and running. This process might take several minutes to complete.

2. To access the Rancher UI, go to `http://<SERVER_IP>:8080`, replacing `<SERVER_IP>` with the IP address of your host. The UI displays a Welcome page.

   > **Note:** If you are running your browser on the same host running Rancher server, you will need to use the host’s real IP address, such as `http://192.168.1.100:8080,` and not `http://localhost:8080` or `http://127.0.0.1:8080`.

3. Click **Authenticate with GitHub**. The Rancher UI displays.

### Working with Your First Environment

Rancher supports grouping resources into multiple [environments]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/concepts/environments/). Each environment starts with an [environment template]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/concepts/environment-template) that defines its set of [infrastructure services]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/concepts/infra-services/), and one or more users or groups own it.

In addition to Docker Swarm, Kubernetes, and Mesos, Rancher supports its own container orchestration and scheduling framework called **Cattle**. Rancher uses Cattle extensively to orchestrate infrastructure services, as well as to set up, manage, and upgrade Swarm, Kubernetes, and Mesos clusters.

Initially, Rancher creates a **Default** Cattle environment for you. You can also create a new environment that has an environment template set as another container orchestration. If you want to create a [Kubernetes]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/tasks/environments/kubernetes/) environment, see our [Quick Start Guide for Kubernetes]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/quick-start-guide/kubernetes/) to continue.

### Adding Hosts

The process of adding hosts is the same steps for all container orchestration types. You can either add a host from a cloud provider that Rancher v1.6 supports, or you can add a custom host. If you don't see your cloud provider in the UI, don't worry. Simply use the custom host option.

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

   >**Note:** The IP address in the command must match your `<SERVER_IP>` and it must be reachable from inside your host.

5. Copy, paste, and run the Docker command on your host to register it with Rancher. This process might take a few minutes to complete.
6. Click **Close**. On the Hosts page, you can view the status of your host.

After you add at least one host to your environment, it might take several minutes for all Rancher system services to launch. To verify the status of your environment, from the **Environment** menu, select **Manage Environments**. If a service is healthy, its state displays in green.

### Adding Containers

Once you've verified that all system services are up and running, you're ready to create your first container. To do so, create a stack and then add services to it. A `stack` is a convenient way to group services together.

The process for adding a container differs depending on your container orchestration type. In this guide, we're going to cover adding containers to your Cattle environment. For information on adding containers to other container orchestration types, see [Adding Containers]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/tasks/stacks/adding-containers/).

#### To Add a Container:

1. On the Rancher UI menu, click **Stacks**. (Or, from the Welcome screen, click **Define a Service**.)
2. Click **Add Stack**. The Add Stack page displays.
3. Enter a **Name**, such as `first-container`.
4. Click **Create**. The new stack displays on the Stacks page.
5. Click **Add Service** in the new stack, and then enter a name, such as `first-service`. Rancher launches the container on the host. This process might take a few minutes to complete. You can view the status of the container on the Stacks page.

Regardless of your host's IP address, `first-container` will have an IP address in the `10.42.*.*` range, as Rancher has created a managed overlay network with the `ipsec` infrastructure service. This managed overlay network is how containers can communicate with each other across different hosts.

From the `first-container` drop-down list, you can perform management actions, such as stopping the container, viewing the logs, and accessing the container console.

### Launching Catalog Applications

To help you deploy complex stacks, Rancher offers a catalog of application templates.

#### To Launch a Catalog Application:

1. On the Rancher UI menu, click **Catalog**, and then select one of the following:
   * **All** -- This option displays both Rancher Certified Library and Rancher Community catalog templates.
   * **Library** -- This catalog provides Rancher Certified Library templates for core and infrastructure services. It’s maintained and [certified by Rancher Labs](https://github.com/rancher/rancher-catalog).
   * **Community** -- This catalog provides templates for any application. Templates are created and maintained by the [Rancher Community](https://github.com/rancher/community-catalog), not certified by Rancher Labs.
2. Search for the template you want to launch, and then click **View Details**.
3. Select a **Template Version**. The latest version displays by default.
4. Enter a **Name** (required) and **Description** (optional) for your template.   
5. Enter the **Configuration Options**, which are specific to the template.

    >**Note:** To review the `docker-compose.yml` and `rancher-compose.yml` files used to generate the stacks, click **Preview** before launching the stack.

6. Select **Start services after creating**.
7. Click **Launch**. On the Stack page, you'll see Rancher is creating a stack based on your new application. This process might take a few minutes.

Once your new stack's services are up and running, its state displays in green.

### Next Steps

Now that you've added hosts, created your first container, and launched a catalog application, you can check out the rest of the features in Rancher v1.6:

* [Setup]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/setup/)
* [Concepts]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/concepts/)
* [Tasks]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/tasks/)
* [FAQ]({{site.baseurl}}/rancher/{{page.version}}/{{page.lang}}/faq/)
