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


