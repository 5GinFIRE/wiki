<!-- TITLE: 5G Driving Trainer -->
<!-- SUBTITLE: A quick tutorial on how to start 5G-DT VNF service -->

# Introduction
The 5G Driving Trainer (5G-DT) system consists of a proof of concept prototype able to perform real-time image processing of moving vehicles and elaborating solutions for safe, correct and ecological driving behavior. 
The experimentation includes VNF instances for the 5G-DT data collection and processing along with the 5G-DT edge computing system, deployed at the edge of the infrastructure. Such an hybrid architecture permits, on the one hand, to efficiently process video streams closer to the sources and on the other hand, to take advantage of the distributed cloud 5GINFIRE infrastructure for global data aggregation and data fusion. 
Interaction with OBU is experimented to evaluate the overall system performance and capabilities including the assessment of user experiences. Specific monitoring probes are also deployed to accurately collect and analyze latency performance.
 

# Experiment architecture
The experimental network setup is depicted below. The cloud infrastructure directly communicates with the Road Side Units (RSUs) through wired connection. The Smart cameras are connected over the same subnet through a network switch. The moving vehicle hosts an On-Board Unit (OBU), interconnected to the 5GinFIRE infrastructure through a 802.11p/WAVE interface. 
Video feeds flow from the IPcams to the relative ECB, then visual descriptors are sent via RSU to the 5G-DT VNF interface. The Local Mobility Anchor (LMA) instance acts as management panel to retrieve diagnostic information and connect the RSU to the external network. 
  

# Running the experiment
The VNF machine already contains the software. The VNF application is based on a Python Flask server that collects the information from the edge nodes, a MySQL database to store the gathered data and a python app to send the notification to the mobile devices. 3 Steps are required to run the application, as described below.

## Connecting to the VNF machine
1.        Connect to the network where the VNF machine is deployed
2.        Connect to the VNF over SSH.
3.        VNF credentials are as follows:
        user name: ubuntu
        password: ubuntu
## Starting the OBU
The OBU can be simulated by a HTTP/TCP server in a smartphone. To start the OBU, download a HTTP/TCP server from the Playstore or Apple store and run the application with the IP address that belongs to the same VNF network
1. Instantiate a HTTP/TCP server


## Run the application
1. Move to python flask to start the HTTP server

>     cd flask_server/

2. Run the flask server

>     python3 main.py

4. Move to the processing application

>     cd ../processing_task/

5. Run the processing application + parameters

>     python3 main.py <maxspeed> <OBUIP> <OBUTCPport>

\<maxspeed> is the maximum vehicle speed threshold.
\<OBUIP> is the OnBoard device IP from the vehicle subnet
\<OBUTCPport> is the TCP port opened on the OBU server

NOTE: the VNF requires connection with the Embedded Computing Nodes (ECBs) installed within the IT-Aveiro test site. These devices generate the metadata to be processed by the 5G-DT VNF instance. 
