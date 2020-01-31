<!-- TITLE: ADOCKER -->
<!-- SUBTITLE: ADOCKER is a platform for the management of a distributed computing platform for Docker containers using Kubernetes -->

# 1. Introduction
ADOCKER is a platform that allow experimenters to set up their own testbed for experimenting on 5GINFIRE. This new platform is based on a Kubernetes cluster and is able to be deployed automatically. It uses two types of nodes: the workers, where the VNFs are deployed, and the master which manages the infrastructure and connects it to the 5GINFIRe platform. The master node is permanent, while any number of worker nodes can be added from remote places and deleted once they are no longer needed. All of this is done automatically and no tedious work or deep knowledge is required from the experimenters. The integration with the OSM is guaranteed by the ADOCKER middleware and the VIM plugin.


