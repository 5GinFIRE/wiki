<!-- Example: Software Defined Radio Slices with HyDRA Experiment -->
<!-- SUBTITLE: Software Defined Radio Slices with HyDRA Experiment -->

# Overview
WINS_5G, the reconfigurable radio testbed at Trinity College Dublin, provides virtualized radio hardware, software virtualisation, Cloud-RAN, Network Functions Virtualisation (NFV), and Software Defined Networking technologies to support the experimental investigation of the interplay between 5G radio and future networks. This 5GINFIRE testbed adds the capability to instantiate network function virtualisation (NFV) experimental vertical instances (EVIs) to the radio access network supported by the radio slicing and virtualisation layer called Hypervisor for Software Defined Radios (HyDRA), developed by Trinity College Dublin researchers. This experiment, which can be carried out using the WINS_5G infrastructure supported by deployed HyDRA As A Service (HyDRA-AAS) Network Service Descriptor (NSD) and Virtual Network Function Descriptor (VNFD), will describe the process of deploying and experimenting with 5G radio slices. The HyDRA-AAS NSD and VNFD used in this experiment are available from the 5GINFIRE Market place. WINS_5G is ideally equipped to investigate the combination of network slicing, virtualisation functions, and physical layer approaches into coexisting and coherent next-generation commercial networks, including, but not limited to, 5G.
# System Architecture – The Baseline WINS_5G Radio Slicing EVI
The baseline WINS_5G EVI contains the following elements: 
1. two radio access technologies sharing the same physical device, 
2. HyDRA-AAS to slice the physical radio device into the two radios, and 
3. two receiver devices (one for each radio slice).

To this end, we have built the baseline WINS_5G EVI comprising a total of 4 VNFs, as follows:
* 1 x VNF that implements the two radio access technologies in two independent GNURadio processes (named EVI1 and EVI2);
* 1 x  VNF implementing HyDRA-AAS; 
* 1 x  VNF to receive signal from EVI1;
* 1 x  VNF to receive signal from EVI2.

The four VNF and their interaction are shown in Figure 1. The Client VNF implements the baseband processing for EVI1 and EVI2. This VNF is connected to the HyDRA VNF, which runs the HyDRA-AAS Server as described earlier in Section 1. HyDRA VNF interacts directly with a USRP device to transmit the multiplexed signal of EVI1 and EVI2. Finally, the two VNFs, named EVI1 RX and EVI2 RX, receive the data for their corresponding EVI. Each one of these VNF is connected to a USRP for signal reception. 


![Hydra Experiment](/uploads/hydra-experiment.png "Hydra Experiment")
### Figure 1 5G Radio Slicing EVI Experiment supported by HyDRA - four VNFs and their interaction

# Recreating the Experiment
By launching the HyDRA-AAS NSD and VNFD services, the experiment will automatically be setup, configured, and started.
# HyDRA-AAS: The Client for Radio Resource Management Functions
We implemented a Radio Resource Management Functions (RRMF) in HyDRA-AAS to support EVI assess to available physical radio resources and to request the creation of new vRF front-ends. Descriptions and examples of HyDRA RRMF are shown below. Experimenters can send a JSON request to the HyDRA-AAS server on port 5000 (default). 

RRMF name: check_connection	
Description: Check if the HyDRA-AAS server is up and running. If yes, HyDRA-AAS will reply, otherwise the message will timeout.	


RRMF name: query_resources		
Description: Returns a list of tuples in the form (CF, BW) of all portions of radio spectrum available to use by HyDRA. Note: this portions can be in use by external radio access technologies.	

RRMF name: request_tx_resources	
Description: Creates a new vRF front-end and virtual network interface. This slices the physical USRP with a new vRF front-end with TX only capabilities. The virtual network interface is used to provide HyDRA-AAS functionalities.	

RRMF name: request_rx_resources		
Description: Creates a new vRF front-end and virtual network interface. This slices the physical USRP with a new vRF front-end with TX only capabilities. The virtual network interface is used to provide HyDRA-AAS functionalities.	

The type of data presented in the JSON column is as follows:
* u_id: integer. Identifies the ID of the client. Each client connected to a HyDRA-AAS has a unique ID starting from 1.
* d_centre_freq: double. The centre frequency in which the virtual RF front-end will operate. Valid values should be in the list of tuples obtained from query_resources. For each tuple, a valid value is between CF-BW/2 and CF+BW/2.
* d_bandwidth: The bandwidth in which the virtual RF front-end will operate. Valid values should be in the list of tuples obtained from query_resources. For each tuple, a valid value is equal or less than BW.
* bpad: boolean. If HyDRA-AAS should add padding samples for the TX buffers. Advanced configuration. The default is False.

Operations to change the resources in real time, such as changing the center frequency or the bandwidth, can be accomplished simply the performing a request_tx_resources or request_rx_resources with the new configuration wanted and the same id. 

# HyDRA Client Library 


