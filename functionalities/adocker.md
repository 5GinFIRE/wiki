<!-- TITLE: ADOCKER -->
<!-- SUBTITLE: ADOCKER is a platform for the management of a distributed computing platform for Docker containers using Kubernetes -->

# 1. Introduction
ADOCKER is a platform that allow experimenters to set up their own testbed for experimenting on 5GINFIRE. This new platform is based on a Kubernetes cluster and is able to be deployed automatically. It uses two types of nodes: the workers, where the VNFs are deployed, and the master which manages the infrastructure and connects it to the 5GINFIRe platform. The master node is permanent, while any number of worker nodes can be added from remote places and deleted once they are no longer needed. All of this is done automatically and no tedious work or deep knowledge is required from the experimenters. The integration with the OSM is guaranteed by the ADOCKER middleware and the VIM plugin.

# 2. Functionalities
The ADOCKER platform must be installed on premise by the experimenters and consortium members wanting to use it. Due to the fact that there is no public IP available for the ADOCKER master node on the 5TONIC infrastructure (only VPN connection is available) and because the ADOCKER middleware needs to be exposed to allow the remote nodes to connect with it, the master node must be deployed outside the 5TONIC infrastructure and connected through the VPN as any other testbed (VIM). Installing the master node inside 5TONIC will imply that every remote/worker node which is wanted to be added to the platform will need a separate VPN connection, creating an undesirable overhead for the 5TONIC administrators. This means that no ad-hoc infrastructure management can be directly connected to the 5TONIC infrastructure, so an external proxy is needed.

To overcome this problem, an automated installation method has been created to simplify this procedure. This allows the on premise deployment of ADOCKER, while keeping the option to deploy it within 5TONIC infrastructure if it’s possible in the future. With this type of deployment, each ADOCKER instance needs only one VPN connection with 5TONIC. This allows any number of worker nodes to be added directly to the master node, which will act as a proxy to the 5TONIC infrastructure.

## 2.1. On premise installation of ADOCKER platform
To allow the experimenters and other users the ADOCKER deployment and usage,  the installation procedure of the infrastructure has been fully automated. The user just needs to fill a configuration file and run the installation script, which will do all the work to setup and configure the infrastructure and connect it to the 5GINFIRE platform as another testbed.

There can be multiple ADOCKER instances of the platform and any ADOCKER user may install any number of them. Each of the instances is fully independent and will act as a different cloud platform, so to connect them to the 5GINFIRE platform a new VPN credentials and address space must be provided by the 5TONIC administrators. Each instance runs on the user’s hardware, which needs to guarantee the connectivity between the worker nodes and the master by itself.

### 2.1.1. Recommended hardware
For the installation of the ADOCKER master node, at least 4 CPUs and 8 GBs of RAM is recommended. This node will only do management and orchestration tasks, no VNFs will be deployed on it. However, as the number of master nodes grows, the requirements for these tasks increases. Moreover, this node will be the only one connected to the 5GINFIRE VPN, increasing the network load on it.

For the remote worker nodes, the requirements depend on the VNFs which are going to be deployed. To make room for the management services deployed on the remote nodes, and for some VNFs on top of them, at least 2 CPUs and 4 GBs of RAM are recommended.

These recommendations are highly dependent on the type and age of the hardware. No performance measurements have been done in low-end hardware, so be aware that it might be performance issues on some types of devices.


## 2.2. Ad-hoc infrastructure management
With the master node of the ADOCKER Platform installed, new worker nodes can be added to the platform. These are the nodes where the VNFs will run, it’s important to highlight that without adding worker nodes to the platform no workloads (VNFs) will be scheduled on the platform. The master node is just used to the management and orchestration of the infrastructure and its services, no customer workloads can be added to this node.

The process of adding new remote/worker nodes to the infrastructure has also been automated, and it can be performed with just one docker run command. Any number of nodes can be added in this way and the workloads (VNFs) will be scheduled on them. It is also possible to remove the nodes at any moment or add them later again. The VNFs running on these nodes will be scheduled to other nodes if there are enough availability. If there is no more nodes available, the workloads will be paused until there is enough hardware available.

The connection between these node is done through a VPN, this a different VPN from the 5TONIC-hosted VPN, and it is only set between the remote nodes and the ADOCKER master node. The connection to this VPN is fully automated, the user does not need to do anything else than running the command which joins the remote node to the platform. Thanks to this VPN the nodes can be added anywhere as long as the remote node is able to connect to the ADOCKER middleware and the VPN server.

## 2.3 VNF deployment on ad-hoc infrastructure
Once the nodes are joined to the infrastructure, they are added to a Kubernetes cluster, managed by the ADOCKER middleware. The middleware adds some labels to the kubernetes node in order to schedule the workloads in the right nodes. The remote nodes are labeled as “cloud=remote” while the master node is labeled as “cloud=private”. The architecture (arm, amd64, etc.) of the node, the operative system, the hostname, and so on are also labelled. Hence, the middleware can schedule the workloads (VNFs, NSs, etc.) to de deployed on the nodes where they should run on.

The deployment of the VNFs and NSs is done by the 5TONIC hosted OSM vía a VIM plugin. This plugin has been developed by Gradiant and makes use of the ADOCKER middleware REST API. It has been designed to emulate the behavior of OpenStack, or any other cloud provider compliant with the OSM VIM plugin interface. This is especially challenging in a Kubernetes environment, as he VIM plugin interface has been designed with VMs in mind and there is no direct (one to one) equivalence between the management of a Docker container (Kubernetes pod) and a Virtual Machine.

To allow this emulated management, the VIM plugin has to translate many of the methods in the interface to a set of different methods on the ADOCKER middleware API. The middleware then translates them to the Kubernetes native configuration. In addition, the default behaviour of Kubernetes has been modified with some plugins in order to close the difference between VMs and Containers for the VNFs, and not just for the OSM that is orchestrating them. These plugins consist mainly of CNI plugins for the network management in the VNF deployment. The plugins are installed and configured to allow the creation of isolated virtual network which can be connected to the containers through a network interface.

Although the combination of the different plugins should be able to provide an unlimited number of network interfaces to the containers, this technology has proved no to be yet fully functional, and there are limitations in the way the virtual networks can be created and added to the containers. At this moment, the ADOCKER platform has a limitation of two network interfaces per container, one used for the data plane and another used for the control plane. This make not feasible to deploy some VNFs like routers or switches. The VNFs developers and experimenters must take this in consideration, as well as other limitations that come with the deployment of Docker containers through this platform.

## 2.4 Monitoring framework
A basic monitoring framework (previously developed by Gradiant) has been already integrated in some of the tools used to develop the ADOCKER platform. As this functionality seemed to be helpful, it has been included in ADOCKER platform. However, the integration into 5GINFIRE or OSM monitoring stack is not included.
This monitoring framework allows to know the load created by any of the VNFs and systems running on the platform. These load measurements include the RAM, CPU and network load, and can be visualized on a time graph, using time windows of different size and starting on different time instants.

# 3. User manual
This sections explains how to deploy, manage and connect ADOCKER to the 5GINFIRE platform.

## 3.1. Installation process
The ADOCKER platform is based on two different types of nodes. The master node and the remote/worker node. The first one is where the management services and the kubernetes master are deployed. This is also the node that has to be joined to the 5GINFIRE VPN and connected to the 5TONIC hosted OSM. The second one is where the VNFs are deployed. It may be any number of remote/worker nodes and they can be far from the master node. They can be also easily added and removed from the platform at any time and any number of times.

### 3.1.1. Master node installation
The installation of the master node is fully automated and is performed through an Ansible [5] playbook. Before the installation we only need to configure the adocker_config.ini configuration file before launching the playbook


```text
[MASTER_NODE]
host=123.123.123.123

user=ubuntu

# if not provided, passwordless communication between this
# computer and the masternode must be enabled. Not needed
# if host=localhost.
ssh_pass=ubuntu_password
become_pass=sudo_password

[ADOCKER]
# Admin is not allowed
username=adocker_username
password=adocker_password

[5TONIC_VPN]
addr_pool=10.154.100.0/24

ca_path=/path/to/the/ca/certificate/file

client_cert_path=/path/to/the/client/certificate/file

# don’t set a password for the key file
client_key_path=/path/to/the/client/key/file

# if not set a basic template will be used
client_conf_path=/path/to/the/client/configuration/file

# if needed
ta_file_path=/path/to/the/ta/file

```

Once this configuration file is filled with the required parameters, we can launch the Ansible playbook.


```sh
ansible-playbook master_install.yml
```


The playbook will install all the needed software in the given host and setup the ADOCKER platform. If there is any problem during the installation of the ADOCKER platform the playbook will be stopped and some information about the problem will be provided. Please verify that the problem is not a misconfiguration or it is not caused by a problem in your infrastructure before asking Gradiant for support.

Once the playbook execution ends, the ADOCKER platform will be ready to start adding worker nodes, but before deploying VNFs through the OSM, the OSM must be configured with this new VIM in the 5TONIC infrastructure.


### 3.1.2. 5TONIC installation for the new VIM
The functionality of ADOCKER is added to the 5TONIC OSM as a VIM plugin. It becomes necessary to install that plugin to proper function. The following steps guide you through the installation process of the VIM plugin.

The administrator of the OSM platform have to get the VIM Plugin files:
*  `vim_adocker.py`
* ` adocker_client.py`
*  `adocker_api-1.0.0-py2.7.egg`

These files will be provided by Gradiant to the 5GINFIRE consortium and the 5TONIC administrators. This action will be performed only once, the first time the ADOCKER VIM is installed on the OSM instance. The administrator has to copy these files to the installation folder of the OSM platform `<OSM_INSTALL_FOLDER>`.

`<OSM_INSTALL_FOLDER>/openmano/osm_ro/`

The next steps are to configure OSM to recognize and use the VIM plugin.

First, create an Openmano tenant, if not already done with this command:


```sh
$> openmano tenant-create osm
```


And export its name as environment variable:


```sh
$> export OPENMANO_TENANT=osm
```


Next, execute datacenter-create and datacenter-attach instructions to add the ADOCKER functionality, where `<access-URL>` is the URL where ADOCKER Middleware is listening, and `<tenant-user->` and `<tenant-password>` are the credentials set on the adocker_config.ini file in the [ADOCKER] section.


```sh
$> openmano datacenter-create adocker <access-URL>  --type=adocker
$> openmano datacenter-attach adocker \
--vim-tenant-name=”osm” \
--user=<tenant-user> \
--password=<tenant-password>
```


These actions, except copying the VIM Plugin files in the OSM installation folder, must be performed every time a new ADOCKER instance is configured, as they are different VIMs of the same type.

## 3.2. Infrastructure management
Once the OSM is configured the ADOCKER instance can start receiving requests to deploy VNFs from the OSM. However, ADOCKER needs to set up at least one remote/worker node in order to schedule the deployment of the VNFs. The addition and deletion of the remote worker nodes is done through a small web interface exposed in the ADOCKER master node in the 80 port.

### 3.2.1. Web interface overview
To access the web interface we need to enter the credentials previously set in the `adocker_config.ini`.

![Figure 1: Web interface login](/uploads/adocker/adocker-1.png "Figure 1: Web interface login" =250x)
Figure 1: Web interface login

The main panel contains the information about the hardware we have available on the ADOCKER platform. By default there are no remote nodes, so the interface does not display any resources.

![Figure 2: Main panel](/uploads/adocker/adocker-2.png "Figure 2: Main panel")
Figure 2: Main panel

From the left panel we can browse the different sections of the interface, like the configuration or the monitoring sections.

# 3.2.2. Adding new nodes
To add new nodes we need to click the “join node” button, then a popup will give us the command we need to run in the node we want to join to the ADOCKER platform. This node must be a Linux based machine and have Docker installed.

![Figure 3: Join node button and command](/uploads/adocker/adocker-3.png "Figure 3: Join node button and command")
Figure 3: Join node button and command

Simply copy the command onto the linux terminal and let it run for a while, depending on the speed of the internet connection it may take longer or shorter. In general it does not need more than a few minutes to complete. Once the execution is complete the computer where this command ran is part of the ADOCKER platform and will be shown on the main panel.

![Figure 4: Joined node preview](/uploads/adocker/adocker-4.png "Figure 4: Joined node preview")
Figure 4: Joined node preview

From now on, the VNFs will be deployed on this node. To delete a node from ADOCKER you just need to click the cross in the right upper corner of the node (red box identified in the next figure).

The node will be deleted from the platform and no other VNFs will run on it. You can disconnect this node from the network or shut it down. The containers and services responsible for the connection of this node to the platform are still running, you can remove it using the Docker rm command. In the current version, it is not possible to stop and remove these containers and its images from the master node, so it must be done manually. If this node join again to the platform in the future, this will be cleaned automatically during the joining process.

### 3.2.3. Infrastructure and VNFs monitoring
The web interface also offers a basic monitoring panel to monitor the infrastructure and the services running on it. It makes use of the Grafana [6] technology to show the data recovered from the Kubernetes monitoring framework. In the third button on the left panel on the main window we can access the monitoring section.


a) Upper graph: CPU load b) Middle graph: RAM usage c) Lower graph: Network load

Here we can filter the services and namespaces from where we want to get the monitoring data. The data we can gather are from the CPU, RAM, and the network load. Also we can modify the time window of the displayed data.

To use these filters we must select the namespace and the pod we want to monitor in the dropdown lists on the left upper corner. In the namespaces we can select the infrastructures related (kube-system, ns-adocker, openvpn, etc.) or the user namespace where the VNFs are deployed. It is possible to select all the pods on the namespace to compare their loads or select just a pod. These load measurements are shown in a graph where can be seen their evolution in time. To select another time interval, use the options in the top right corner to zoom in/out or slide the time interval backwards or forward. A list of preset options (24h, one month, etc.) can be also selected.


## 3.3. ADOCKER usage
The infrastructure deployed following the steps described on the previous sections can be used to deploy VNFs through the OSM. For 5GINFIRE this is scheduled uploading an NSD to the 5GINFIRE portal, and selecting your new ADOCKER infrastructure as the target VIM. However, since ADOCKER is based on Kubernetes and Docker, there are some distinctiveness of the infrastructure that must be considered in this process.

### 3.3.1 VNF development
VNFs deployed on ADOCKER must be running on Docker containers. ADOCKER infrastructure is based on Docker (Hypervisor) and Kubernetes (Orchestrator), so it can handle only Docker containers. If the VNF consist on a service running inside a VM, it may be easy to turn it into a Docker container, but if it’s more related to the VM kernel or architecture it may become hard to achieve. Also, if the VNF is a new development, it’s easier to design it to be easily adaptable to this infrastructure. In any case the VNF developers are expected to adapt their VNFs to the platform where they are going to deploy them.

### 3.3.2 Networking
Due to the limitations on the ADOCKER networking technologies there are some features that are not available:
Isolated networks: PARTIAL SUPPORT. You can create as many networks as needed, each network is isolated and they support broadcasting. Level 2 connectivity is only available on single node deployments, so don’t expect it to work in any case.
Multiple NICs per container: PARTIAL SUPPORT. Due to the limitations of the underlying CNI technology, multiple NICs can be added to a container, but only two networks can be attached to the same container. If more NICs are added, they must be assigned to the same networks. However, this limitation is seen as a bug by this CNI technology developers, so it’s expected to be solved at some point in the future.
Service Function Chaining: NOT SUPPORTED. No available networking solution for Docker/Kubernetes is able to offer this feature, so it’s not supported.

### 3.3.3 Container-VM environment
ADOCKER does not allow the deployment of VMs in its infrastructure. Although, since its connected to the 5GINGIRE VPN, an hybrid Container-VM environment can be achieved using multi-site deployment. If a more closed hybrid environment is needed, there are two other ways of achieving it. However, be aware that they are not recommended as they can produce unstable behaviours on the infrastructure, the VNFs and the network connectivity. 

The first way is to add a VM as an ADOCKER worker node. In this case the containers will be deployed on a VM running alongside the VM-based VNFs. The second one is to add as an ADOCKER worker a machine with a VM hypervisor installed. In this case the VMs and the containers will be running on the same hardware. The main flaw with both approaches is that the VMs and the containers are managed by two different systems, without knowing about the existence of each other.

# References
* http://5GINFIRE-5g.eu/ © 5GINFIRE consortium 2017
* https://www.docker.com/ Docker
* https://kubernetes.io/ Kubernetes
* https://osm.etsi.org/ Open Source MANO
* https://www.ansible.com/ Ansible
* https://grafana.com/ Grafana

