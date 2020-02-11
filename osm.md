<!-- TITLE: The 5GinFIRE MANO platform -->
<!-- SUBTITLE: A description of the 5GinFIRE MANO platform -->

# The 5GinFIRE MANO platform
The 5GinFIRE MANO platform is the system that enables the management and orchestration of network services (NS), potentially composed of multiple VxFs, across the NFV experimental infrastructures provided by 5GinFIRE partners. To fulfill this objective, the platform offers a northbound interface to the 5GinFIRE portal, enabling the operations that are needed to support the execution of experiments (e.g., onboarding a NS and a VxF). A simplified overview of the 5GinFIRE MANO platform is depicted in the figure below.

![Mano Platformv 2](/uploads/5-tonic/mano-platform-v-4.png "Mano Platformv 4")

The platform is based on the ETSI-hosted [Open Source MANO (OSM) project](https://osm.etsi.org/), which has been adopted by 5GinFIRE to provide the functionalities of a NFV orchestrator, supporting the management and coordination of computing, storage and network resources at the diverse experimental infrastructures, as well as the lifecycle management of network services. With this purpose, the OSM stack interacts with the Virtualized Infrastructure Managers (VIMs) deployed by 5GinFIRE partners. 

The MANO platform supports multi-site experimentation activities across different vertical domains (a more comprehensive description of the available infrastructures can be found [here](http://wiki.5ginfire.eu/5GinFIREtestbeds)):

1)	An infrastructure at the global 5G Telefonica Open Innovation Laboratory (5TONIC), made available by TID and UC3M, to support experimentation with NFV functions and services.
2)	An experimentation facility located at ITAv, providing access to an automotive testbed in the city of Aveiro (Portugal).
3)	An infrastructure made available by UNIVBRIS, supporting experimentation activities over a smart city environment in the city of Bristol (UK).
4)	An experimentation facility at UFU, located at Uberlândia (Brazil), enabling trialling with 5G applications with a particular consideration on the edge network resources.
5)	An infrastructure provided by the NITOS testbed, i.e., 5GVINO, hosted by the University of Thessaly (Greece), which provides access to programmable resources for wireless networking, SDN and cloud computing facilities.
6)	An eHealth experimental vertical facility, eHealth5G, hosted by the Poznan Supercomputing and Networking Center (Poland); this facility supports experimentation in the area of telemedicine and eHealth, offering access to: realistic eHealth equipment; a small Edge Cloud, close to eHealth devices; and a core cloud accessible via MPLS/Optical service provider network.
7)	A reconfigurable radio testbed at Trinity College Dublin (Ireland), Iris, supporting radio hardware, cloud-RAN, NFV, and SDN technologies. This testbed has been extended and made available for experimentation activities in 5GinFIRE. We refer to this testbed extension as WINS_5G.
8)	A development, testing, and verification facility for experimentation with 5G network architectures and services for public protection and disaster relief, PPDR ONE, made available by the INTERNET INSTITUTE at Ljubljana (Slovenia). The facility also includes a portable compact mobile system that can be deployed on experimenters’ test sites.
9)	A testbed facility targeted at the 5G media vertical industry, i.e., 5G Media Vertical, provided by TNO (Netherlands).

The current version of the 5GinFIRE MANO platform is based on OSM Release FIVE, being the OSM stack hosted at 5TONIC. The Release FIVE that is working in production state includes [specific modifications](http://wiki.5ginfire.eu/guide/5ginfire-contributions) to enable the multi-site deployment of network services under consideration in 5GinFIRE. Each partner running an experimental infrastructure is in charge of the deployment and maintenance of one or several Virtualized Infrastructure Managers (VIMs), compliant with the OSM software stack. On top of that, the NFV orchestrator of 5GinFIRE, deployed at 5TONIC, interacts with the VIMs of the infrastructure providers involved in a service deployment: it coordinates the allocation and setup of the computing, storage and network resources which are necessary for the instantiation and interconnection of the VxFs that compose the network service.

Inter-site communications among 5GinFIRE sites are enabled by an overlay network architecture based on VPNs (to simplify operations, the primary VPN server is hosted at the same site as the MANO stack, i.e. 5TONIC). This overlay network provides authorized partners a secure access with certificate-based authentication to 5TONIC, enabling the establishment of the following types of data exchanges: (a) communications between the OSM stack and the VIMs; (b) communications between the OSM stack and the VxFs, to support their configuration; (c) inter-site data communications among VxFs. 

The technical solution adopted by 5GinIFIRE supports the flexible incorporation of other sites (e.g., those that have come from the Open Call process of 5GinFIRE), as long as they support a compliant VIM[^1] and they set up the appropriate VPN based inter-site connection mechanisms (see the information below).

[^1]: OSM Release FIVE supports multiple types of VIMs through a plugin model, including OpenVIM, OpenStack, VMWare vCloud Director, and Amazon Web Services Elastic Compute Cloud.


## Addressing plan
Enabling effective network communications among multiple sites has required a careful definition of the IP address space to be used by interconnecting entities. In this respect, the following agreements have been taken by 5GinFIRE:

1)	5G infrastructure providers will use the private address space 10.154.0.0/16 for control and data plane communications.
2)To simplify routing configurations inside 5TONIC, this specific location will use the private address space 10.4.0.0/16 to support control and data plane communications.

The 5GinFIRE network operations centre is in charge of the allocation of IP address ranges to entities within the address space 10.154.0.0/16. The current allocation is shown in the table below.

- **5TONIC**: 10.4.0.0/16.
- **ITAv**:	10.154.0.0/20, 10.154.128.0/20.
- **UNIVBRIS**:	10.154.16.0/20.
- **UFU**:	10.154.32.0/20.
- **WINS_5G**:	10.154.48.0/20.
- **5GVINO**:	10.154.64.0/20.
- **eHealth5G**:	10.154.80.0/20.
- **PPDR ONE (stationary site)**: 10.154.96.0/21.
- ** PPDR ONE (portable site)**: 10.154.104.0/21.
. **5G Media Vertical**: 10.154.112.0/20.

In addition, the VPN service at 5TONIC has been configured to use subnetworks 10.154.255.0/24 and 10.154.254.0/24, being the latter the address range that has been allocated to VPN endpoints connecting to the infrastructure.

Any potential issues related with the allocation of IP addresses to external sites that connect to the MANO platform will be treated on a case-by-case basis. 

## Requirements to support the interconnection of external sites
Beyond any particular requirements that may be identified when facing the interconnection of specific external sites, which will be treated on a case-by-case basis, in the following we indicate a non-exhaustive list of requirements that must be fulfilled by external entities to connect to the 5GinFIRE MANO platform:

1)	Utilization of a VIM solution compliant with the 5GiFIRE MANO platform.
2)	Utilization of an appropriate IP address space, not conflicting with the address space assigned to the 5GinFIRE infrastructure providers so far. Interconnecting entities must use a range of IP addresses within the network prefix 10.154.0.0/16. This range will be determined by the 5GinFIRE network operations centre, according to existing allocations and the entity’s needs.
3)	Obtaining VPN credentials to connect the entity’s infrastructure to the network overlay architecture of the 5GinFIRE MANO platform. 
4)	Configuration of the site with the allocated IP address space. Deployment of address translation functions in case that this is needed.
5)	Installation and configuration of the VPN endpoints that are necessary at the external site.
6)	Configuration of appropriate VIM networks, to enable the exchange of control and data-plane information originated and terminated at the VxFs deployed at the entity’s datacentre.
7)	Set up of the appropriate mechanisms to support the delivery of control and data-plane information across the local network segments of the external entity (i.e. from the VPN endpoints towards the VIM/SDN controller and VxFs, and vice versa).


