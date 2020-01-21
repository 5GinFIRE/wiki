<!-- TITLE: C 2 Gran -->
<!-- SUBTITLE: C2G-RAN  -->

# C2G-RAN 

## 1	How to Run the Experiment
In order to run the experiment, at least two nodes required to set up in the same subnet. Both must be connected and configured with USRP’s and the RX frequency of one URSP must match the TX frequency of the other USRP and vice versa. Use the GNURadio-companion tool to customize the frequency and channel selection. GNURadio-companion tool is already installed in the VNFs therefore it is not needed to reinstall. GNURadio-Companion (GRC) is a GUI tool which can be activated using following command:
#gnuradio-companion c2gran_setup.grc
GRC simplify the use of GNURadio by allowing us to create python files graphically instead of creating them in code. The above command opens GRC and creates c2gran-setup.grc file as shown in figure below. This setup file can then be used to initiate radio signals with the desired settings. GRC library contains various blocks in the GRC block path. Most blocks are of two types; Source and Sink. The source blocks are used to generate signals while sink blocks are used to listen and receive for any incoming signals. The blocks are managed under various categories as shown. For example if we want to create a UDP server, we will look for UDP sink block under “Networking Tools” category. 


![Figue 1](/uploads/c2gran/Picture-1.png "Figure 1")

For GNURadio to work, we need to create a flowgraph using all the required source/sink blocks. Each block comes with its own properties and parameters. These properties can be changed from defaults to accomplish our desired tasks. Each block also has its own documentation to help users as shown in Figure below:


![Figue 2](/uploads/c2gran/picture-2.png "Figure 1")

The complete block diagram of our experiment on one of the nodes can be seen in Figure 16. This shows how each module of the GNU-Radio implementation interact with each other. Source and sink blocks for TCP and UDP can also be seen in that diagram. TCP_server_sink and UDP_sink blocks enable the machine to run a server that would be listening on a specified port. All the clients in that machine would be connecting on that port and IP. This sink will then forward incoming packets to other end (source blocks). For a TCP_client block to work correctly, there must be a server listening on the target port. In this experiment, VLC streaming server was listening and TCP_sink block (formally called Socket PDU block) connected with that server. This block then started forwarding packets to the TCP source block on the other end (client side). Over there, the client VLC connects with the TCP_source_block of its GNU-radio on designated IP and port. The source block on this side will then forward all packets to the local client which it received from the sink block on the server end. 
We used VLC client/server streaming tools to generate voice traffic. On the server end, there is an instance of VLC started to stream an MP3 audio file at 96kbps encoding rate. During the experiment, the VLC server streamed audio file in a loop and never ended streaming until interrupted by the administrator. The clients can join/register with this VLC server to start receiving voice stream at the specified time. They can leave/stop receiving stream after the specified interval. Theoretically there is no limit for a server to accept the incoming client connections however it depends on the number of ports, CPU and memory availability. Following commands are used to initiate VLC streaming server and client: 
vlc -I dummy audio1.mp3 --sout '#standard{access=http,mux=ogg,dst=10.154.50.83:8080}'
Here 10.154.50.83 is the IP address of the streaming server and 8080 is the port number of the HTTP streaming server on which this stream can be subscribed. 
vlc http://10.154.50.83:8080
Above is the command on client side to receive the audio stream. Using our python script, multiple client streams can be initiated. 
In order to get the metrics e.g. CPU utilization, memory etc, we created our own tool for metrics collection. This tool was written in Python and gives us status information about CPU, RAM, power consumption etc. In this tool, we exploited Linux’s snmpwalk command and collected the desired information through SNMP agents in each server on 60 seconds interval. For example this is the code snippet to collect power consumption through Dell’s iDRAC at IP address 10.154.50.9:


![Figue 3](/uploads/c2gran/picture-3.png "Figure 1")



![Figue 4](/uploads/c2gran/picture-4.png "Figure 1")

## 1.1	Data and Code Files
The code files of the machine learning scripts and streaming generator scripts can be found on the github public repo at https://github.com/ehsanzahoor/c2gran. The data files will be shared with the 5GINFIRE consortium. The data can be made public or kept private according to the consortium and WIT mutual policy and agreement. 

## 1.2	OSM Descriptors
The experiment comprises of 4 virtual machines each containing the basic GNU-radio installation. The VNFDs for all those VMs have been created and on boarded to the 5GINFIRE online repo using portal.  The details of all the 4 VNFDs can be seen in Table 2: VNF Descriptors.
Table 2: VNF Descriptors
VNF Name	Type	Description
c2gran_rx1	VNF	Receiver for USRP1
c2gran_rx2	VNF	Receiver for USRP2
c2gran_client_2tx_2rx	VNF	C2GRAN client with 2Tx and 2Rx chains. 
c2gran_server	VNF	C2GRAN server. Can be used with client VNF
c2gran_basic_nsd	NSD	NSD for Basic C2GRAN Example
On top of these VNFDs, a network server descriptor (NSD) named as c2gran_basic_nsd has also been created and on boarded through the portal.
