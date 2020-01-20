<!-- TITLE: Cv 2 Xinfire -->
<!-- SUBTITLE: CV2XinFIRE Description -->

# CV2XinFIRE Description

The CV2XinFIRE experiment is focused on evaluating newly introduced V2X solutions for real-world applications and scenarios. In the V2X context, the current experiment focuses on the following activities:

* •	incorporation of the latest, rapidly emerging, but practically yet unexplored in real‐world conditions, C‐V2X (or LTEV2X) radio technology
* •	the development and seamless integration of radio control, parameterization and monitoring components into the 5GinFIRE framework as VxFs/VNFs;
* •	the experimentation for evaluation of fundamental radio, network and application‐level performance indicators;
* •	the showcase of multi‐technology proof‐of‐concept vehicular applications upon a virtualized environment.

Taking into consideration the aforementioned activities, the experiment provides the following outcomes:

* •	A configurable protocol stack for LTE‐V2X operating as a software modem will be integrated into 5GinFIRE.
* •	A fully operational C‐V2X OBU built on commodity hardware based on SDR principles will be added into IT‐AV.
* •	A set of VxFs/VNFs implementing parameterization, reconfiguration and monitoring functionalities will be developed, as well as a basic exemplary ITS applications
* •	A campaign of experiments will be conducted in order to specify performance requirements and limitations for all current V2X communication technologies
* 
Organization:

Feron Technologies PC
https://feron-tech.com/

## Components and data flow
The primary high-level concept that CV2XinFIRE tries to accomplish, is to successfully setup a real vehicle to vehicle or vehicle to ‘X’ communication, and gather different statistics, assisted and initiated by the 5GinFIRE Cloud.
The designed solution and involved entities are described by the following figure.

![Figue 1](/uploads/cv-2-x/picture-1.png "Figure 1")

As depicted from the overall experiment configurations figure, there are mainly 2 involved VxFs and 2 OBU sets. The OBUs are already part of the 5GinFIRE platform and there is no need of any kind of re-deployment. The experimenters may have access to the OBUs upon request, however, there are limited interventions/modifications that can be made.
The information flow in the experiment can be described by the following steps. In the presented data flow, the following entities are identified:
•	The ITS stack data generation VxF.
•	Transmitting OBU.
•	Receiving OBU.
•	The measurements and benchmarking VxF
The data flow steps are:
•	Data are generated in a VxF that implements the ETSI ITS stack.
•	The VxF forwards the generated data through the 5GinFIRE testbed and a pre-configured radio network (two possibilities 4G-5G/Open Air Interface, or ITS-G5 depending on the selected configuration).
•	The VxF also sends timestamps (used for KPI extraction) to the KPI extraction function that is executed on the measurements and benchmarking VxF.
•	The OBU receives the data (though a proper interface).
•	The OBU transmit the data via a software radio module (USRP) to the another OBU.
•	Recipient OBU gathers the data from the USRP. It is noted that the two OBU should be closely spaced (in the same wireless range for low-power radio transmission).
•	Recipient OBU processes the data to extract the initial message payload according to the ETSI ITS stack.
•	Recipient OBU sends the data to the measurements and benchmarking VxF
•	VxF processes the data and extracts statistics and KPIs
•	VxF feed Grafana database with the data
•	Grafana displays all the up to date statistics reports and charts in a visualized way


## On Board Units:
The OBUs are running on two physical hosts implemented on Intel NUC barebones. The OBUs have the following networking capabilities:
-	4G-5G connectivity using an LTE-A dongle connected to a private network provided by the IT-AV testbed of the 5GinFIRE platform.
-	ITS-G5 connectivity using the ITS-G5 modem implemented by IT-AV and it is connected to the NUCs through an Ethernet interface.
-	LTE C-V2X connectivity, which is the novelty introduced by the CV2XinFIRE experiment, provided as a software modem through a USRP software radio device.
As it will become clearer, the ITS packet that is generated based on the ETSI ITS stack can be forwarded through any interface available at the OBU. However, since the objective of the experiment is to integrate the LTE C-V2X modem, the procedure description will focus on its use.



## FERON’s in-house LTE-V2X SDR Modem
CV2XinFIRE brings to 5GinFIRE a real-time SDR modem implementation of the ad-hoc LTE-V2X radio access protocol, which can be used by the research and industrial communities for early over-the-air Cellular V2X technology testing in laboratory and field conditions. In essence, the modem is a binary application for the 3GPP D2D/V2X radio technology, including:
•	An optimized real-time implementation of the sidelink transceiver functionality, i.e. a complete Layer 1 and a light MAC, in C/C++, running in typical x86 Linux-based hosts. The implementation is based on an in-house open-source MATLAB library available also to the community through Github (https://github.com/feron-tech/lte-sidelink)
•	An interface with general-purpose SDR boards, such as Ettus USRP B2x0 series boards, for real-time and over-the-air signal transmission and reception.
•	Open interfaces with standard UDP and ZMQ services for building applications on top of the radio implementation
•	Adaptation layers for providing interoperability with 3rd party code/applications, such as higher-layer ITS stacks (e.g. OpenC2X), deep packet-level analysis using Python Scapy or the Wireshark tools, open-source platforms for real-time visualization (e.g. Graphana stack, Traccar GPS tracking software), and practically any custom application using standard UDP socket or ZMQ protocol.
•	A set of easily human-readable text files for full system configuration, i.e. radio protocol-level, RF-level, external interfaces, and KPIs reporting.
The modem may be (indicatively) used as:
•	An LTE sidelink waveform generator, supporting both D2D and V2X signals.
•	The basis for experimenting with live standard-compliant sidelink signals.
•	An end-to-end sidelink link-level simulator and core component of a sidelink system-level simulator.
•	A platform for testing new resource allocation/scheduling algorithms for D2D/V2X communications.
•	A tool for education/training purposes
The modem development and usage lifecycle are shown in the Figure below.


The modem consists of a single binary application and a set of text-based configuration files, allowing configuring different modem operation modes and scenarios. 
The binary runs in standard x86 hosts (various forms are supported such as desktops, laptops or SBCs), equipped with: 
	powerful (at least 4th generation) Intel Core i3, i5 or i7 processors;
	at least 4GB RAM;
	a USB3.0 or a Gigabit Ethernet interface for communication with the SDR board.
	Ubuntu Desktop or Server Edition OS (e.g. 16.04 LTS)
An SDR front-tend is needed for OTA operation:
	The modem is compatible with the USRP B2x0 family (B210/B200 mini) provided by Ettus Research.
	Operating carrier frequency, master clock rate, transmit and receive gains are fully configurable using text files, as reported above.
Configuration of the modem
In order to run and use the C-V2X software modem, the binary file is executed with three arguments indicating three configuration files. Thus, there are three groups of parameters that control the operation of the modem and can be configured through human-readable text files:
•	Radio-protocol level parameters, which mainly configure the time-frequency radio resources used for V2X transmission and reception. As of December 2019, the reference standard version supported is Rel. 14.4.0 (2017-10). 
•	RF/SDR-level parameters, which mainly configure the over-the-air transceiver settings (carrier frequency, system bandwidth, energy detection levels, etc.) and are directly related to the SDR board used, in this case USRP B2x0 series.
•	Runtime-level parameters, which configure experiment-specific operations, such as the sidelink protocol used for data transmission (discovery/communications), debugging options, reporting modes, and more importantly the interfaces with external services, i.e. UDP and/or ZMQ.
In the following tables we provide a list of the main configurable parameters available, a short explanation and an example configuration for each one.




There is also a third option, where a single VxF is used to host all functionalities – operating as data generator as well as a measurement analysis and presentation VxF.
The data generator VxF runs the following tasks: 
-	The modified OpenC2X software that implements the ETSI-ITS stack and more particularly the following components (information on the OpenC2X structure can be found in https://www.ccs-labs.org/software/openc2x/):
o	CAM message generation process. The process creates CAM messages periodically by consuming available position-velocirty information.  
o	DENM message generation process. Similarly to the CAM generation process, the DENM generator creates the messages according to the ETSI-ITS stack. The DENM message generation is event-based depending on the availability of information 
o	A DENM application host framework, where the experimenter can integrate any ITS application that uses DENM messages to disseminate relevant information.
o	A GPS consumer/generator daemon, i.e. a process that either consumes GPS data using the gpsd daemon, reads GPS location from a file, or simulates fake GPS data. In the context of the experiment, the process was modified in order to receive GPS data using ZeroMQ messaging, which means that the system could either consume GPS data through gpsd or consume GPS data arriving through ZeroMQ messages.
o	An OBD consumer/generator process, i.e. a process that is used to consume or generate OBD data. In the current setup, vehicle speed is assumed to be the exchanged information through the OBD in-vehicle network. Similarly with GPS, data can be consumed from ZeroMQ messages, by reading a file or just create fake OBD messages for simulation.
o	A Local Dynamic Map, i.e. a database that stores all data that is either generated by the assumed vehicle or received through the OpenC2X framework. It is based on SQLite. 
o	A process that forwards the ITS messages in network interfaces.
Depending on the experimenter needs, it is possible to change the functionality of the OpenC2X framework through the configuration files. However, it may also require code alteration/addition. The modified OpenC2X source code is available into the VxF, so that any user can investigate it, change it or expand it.

The measurement-benchmarking VxF runs the following tasks:
-	The OpenC2X framework in order to accept and process the messages received by the data generator process. 
-	A KPI collection and extraction service is also added. The process is called appLatency and despite its name is used in order to extract several KPIs beyond latency. The appLatency service consumes data through ZeroMQ originating by a) the OpenC2X data generation VxF, b) the OBU software modems, c) the OpenC2X receiving processes that are running locally. 
-	An Apache server (localhost port 80) that hosts the web service initially developed by OpenC2X that presents in real time all sent (ego vehicle) and received ITS messages, as well as the carried content, and present in a map (using Open Street Maps) the “position” of the assumed vehicles (ego and other vehicles participating in the experiment).
-	A Grafana web service (localhost port 3000) that is used to store and visualize the extracted KPIs. The KPIs are presented in panels created on the Grafana dashboard. The data are stored in a Influx database named statsdemo.
 

## Before running the experiment:
The VxFs are built on Ubuntu Server 16.0.4 images.
In the CV2XinFIRE image,  all the dependencies are already compiled and pre-installed. In the following text, all required steps to run the components are presented. However, before beginning, the experimenter should:
•	know the IP address of the VxFs.
•	know the credentials in order to connect through SSH to the images. By default, the user/password is (all VxFs): feron/feron 
•	have requested from the IT-AV to prepare the OBUs and set them on-line and ready for experimentation. 
•	request from IT-AV that the VxFs should have internet access. 
•	know the IP addresses of the OBUs.
•	know the credentials in order to connect through SSH to the OBUs. By default for all OBUs, the user/password is: feron/cv2xinfire 


## Connecting to the Virtual machine:
In order to connect to the VxFs after experiment deployment, the following steps should be made:
•	connect to the 5GinFIRE experimenter VPN (any authorized experimenter should have relevant credentials).
•	connect to the jump machine over SSH.
•	from the jump machine’s shell, connect to the VxF over SSH. The default credentials are:
◦	username: feron
◦	password: feron



## Experiment VPN: 
As mentioned before, the experiment results are presented through two web services running on the VxF. However, the fact that in order to access the VxF, a jump machine is used for security purposes, it makes it hard to access through http the specific services. 
In order to bypass the specific problem, the experimenter should use a VPN in order to gain access to the web services. Any VPN server may be used, however, depending on the type, package installation on the VxF side may be required. The approach used in the CV2XinFIRE and that is suggested for other experimenters was the following: 
•	Install an OpenVPN server to a company/institute server with public IP. Since, the VxFs have internet access, it will be possible for them to access the server.
•	Easy installation of OpenVPN server may be performed as described in https://www.cyberciti.biz/faq/howto-setup-openvpn-server-on-ubuntu-linux-14-04-or-16-04-lts/ 
•	When you create the OpenVPN server, a user and a configuration file is also created. The configuration file should be transferred to the VxF. There are several methods to do so (ftp, wget, git, svn and more), however, the simplest should be to create a new file locally on the VxF and copy-past the content of the conf file, i.e.:
◦	Connect through SSH (jump machine) to the VxF.
◦	$ touch myopenvpn.conf
◦	$ nano myopenvpn.conf
◦	Copy the content of the file created on the VPN server and paste it through the SSH terminal to myopenvpn.conf file (use right click or Ctrl+Shift+V in order to paste through an SSH terminal).
◦	Save and exit the file.
◦	OpenVPN package is pre-installed on the VxFs. In order to connect to the VPN, use the command:
$ sudo openvpn myopenvpn.conf
◦	After connecting through the VPN, the user should note the VPN IP in order to use it for web service access. In order to do that:
▪	run $ ifconfig
▪	mark down the IP address of the “tun” interface that has now appeared. Let’s assume that the IP address is vpnIpAddress.
•	In order to access the web services, you can open a browser running on any machine operating in the same VPN and use the following addresses:
◦	http://vpnIpAddress:80  (OpenC2X Apache service)
◦	http://vpnIpAddress:3000 (Grafana web service)
It is noted that only the measurement-benchmarking VxF should be connected to the VPN.




## Initial Checks: 
The two web services are initialized automatically upon VxF image boot. However, it was noticed that sometimes, the services fail to initialize, so the user should check if running, and if not, start the services manually. This can be done as follows:
•	Grafana (systemctl):
◦	$ sudo systemctl status grafana-server
◦	If not running, then execute: 
▪	$ sudo systemctl daemon-reload
▪	$ sudo systemctl start grafana-server

•	Grafana (init.d):
◦	$ sudo service grafana-server status
◦	If not running, then execute: 
▪	$ sudo service grafana-server start
•	Apache (systemctl):
◦	$ sudo systemctl status apache2.service
◦	If not running, then execute: 
▪	$ sudo systemctl start apache2.service
•	Apache (init.d):
◦	$ sudo service apache2 status
◦	If not running, then execute: 
▪	$ sudo service apache2 start


## Starting the C-V2X modems
The software modems are installed on the remote OBU physical hosts. Thus, simple bash scripts are created in order to remotely start the modems. This can be done by the following steps:
•	Browse to the home folder of the data generation VxF and then move in the init_scripts folder 
	$ cd ~/init_scripts
•	Into this folder you should find six configuration files. Three configuration files are for OBU-1 and three configuration files for OBU-2. The configuration files with tx prefix are used from the OBU driven by the data generation VxF, while the configuration files with rx prefix are used from the OBU driven by the measurement/benchmarking VxF. 
•	Initialize and start the OBU-1 modem by running:
$ sudo ./start-modem OBU1-ipaddress tx_sl.cfg tx_usrp.cfg tx_run.cfg
The command requires four arguments. The first argument is the OBD-1 IP address provided by the testbed administrator. The three next arguments are the configuration files that should be located into the init_scripts folder. The configuration files are created based on the instruction provided in the aforementioned paragraph. 
•	Initialize and start the OBU-2 modem by running:
$ sudo ./start-modem OBD1-ipaddress rx_sl.cfg rx_usrp.cfg rx_run.cfg
•	The scripts: 
◦	Copy the configuration files in the OBU hosts.
◦	Execute the software modem binary located into the NUC host folder: ~/lte-sl-modem 
◦	Initialize the OpenC2X process in the remote NUC hosts.

In order to make sure that ssh commands are executed uninterrupted it is suggested to follow the steps below before executing the initialization scripts:
•	On the VxF machine generate key to perform password-free authentication later
$ ssh-keygen
•	Copy the VxF public key to the OBU physical host (remote-host should be replaced by the IP address of the OBU host):
$ ssh-copy-id -i ~/.ssh/id_rsa.pub feron@remote-host


## Starting the OpenC2X instances:
Since the OBU modems have been initiated, the OpenC2X instances should be initialized in both VxFs (data generation and measurement/benchmarking). This is performed by the following simple process:

$ cd ~/OpenC2X/scripts
$ sudo ./runOpenC2X.sh

The latter will open a multi-window terminal with several debug messages regarding the messaging exchange procedure. This is done by following the exact process used in the original OpenC2X project. 
In order to terminate the execution of the experiment, the user should execute in both VxFs the following: 
$ cd ~/OpenC2X/scripts
$ sudo ./stopOpenC2X.sh

The OpenC2X framework is controlled and configured by several configuration files that can be found in the source code. More specifically, the OpenC2X configuration file locations can be found at: 
1. General Configuration File: $ vim ~/OpenC2X/common/config/config.xml
2. CAM configuration file: $ vim ~/OpenC2X/cam/config/config.xml
3. DENM configuration file: $ vim ~/OpenC2X/denm/config/config.xml
4. LDM configuration file: $ vim ~/OpenC2X/ldm/config/config.xml
5. DCC configuration file: $ vim ~/OpenC2X/dcc/config/config.xml
6. GPS configuration file: $ vim ~/OpenC2X/gps/config/config.xml
7. OBD configuration file: $ vim ~/OpenC2X/obd2/config/config.xml

In general, the user should not have to edit the configuration files with the following exceptions.
-	The user should edit the common configuration file. There are several changes that have been made compared to the original OpenC2X project. In order to run the modified OpenC2X framework properly the following steps should be made:
o	Edit the config.xml
o	Set the IPAddress with the IP address of the corresponding OBU as provided by the testbed administrators.
o	Set the ethernetDevice field to:
	cv2x – if LTE C-V2X is to be used for communication
	or the interface name that connects to the ITS-G5 modem provided by IT-AV (usually the ifnames used by the deployed VxFs are ens3 or ens4).
o	Change the experiment number accordingly.
o	Be careful to any change in the port number, since it would also require a change to the port number of a remote engine. 
-	The user should edit the gps configuration file. For real data change the SimulateData field from true to false. The GPS data are expected to port 4343 by ZeroMQ.
-	The user should edit the obd2 configuration file. For real data change the SimulateData field from true to false. The OBD data are expected to port 4345 by ZeroMQ.
The rest of the configuration files should be changed only from users that have experience in the use of OpenC2X.

## Starting the KPI service:
The following procedure initiates the daemon that collects the KPIs and feeds the Grafana tool. The daemon is executed on the measurement/benchmarking VxF only. No special configuration is necessary. 
$ cd ~/build-appLatency
$ ./appLatency



## Accessing the OpenC2X source code:
If the user wants to perform changes in the configuration, functionality and operation of the modified OpenC2X framework that is used by the experiment, then he/she can access the code directly in the VxF. More specifically the code is available in ~/OpenC2X.
After any change is made, the code should be compiled. This can be done as follows:
$ cd ~/OpenC2X/build
$ cmake ..
$ make all


## Accessing OpenC2X http server: 
The OpenC2X http server is accessible by any browser of a machine operating in the experiment VPN in the address http://vpnIpAddress:80/index.html 
The tool consists of multiple dragable and resizable panels. The basic panel is a map updated in real-time from OpenStreetMaps.  By pressing the button “Open Menu”, the user has the opportunity to present panels populated with data that originate by:
•	Ego vehicle CAM messages.
•	Ego vehicle DENM messages.
•	CAM messages from other adjacent vehicles. 
•	DENM messaged from other adjacent vehicles.
As the messages are transmitted from the ego vehicle, by extracting the location data from the CAM, a blue marker appears on the map indicating the position of the ego vehicle. Similarly, as incoming CAM messages arrive, new markers with different color appear (distinction of the messages to different users is made from the station ID). 
In Figure 5, the snapshot presents the ego vehicle (ID: 123456789) and one remote vehicle (ID:123456799), while two panels present in real-time the exchanged content of CAM messages. In case no new message arrives, the last received message will remain open and the position of the remote vehicle will not be updated. 
The experimenter can also access the SQL database that stores all generated and incoming ITS messages. The database is located in 
$ cd ~/OpenC2X/build/ldb/db
The database name is automatically generated as “ldm-ExpNo.db” where ExpNo is the experiment number defined into the common OpenC2X configuration file.



## Accessing Grafana: 
The Grafana service can be accessed using the address http://vpnIpAddress:3000 in a browser hosted by a machine that has access to the experiment VPN.
The default credentials of the Grafana are:
•	Username: admin
•	Password: feron
The KPI dashboard that presents in real time the measurements can be found by pressing:
•	Home (upper left corner)
•	Then press KPI dashboard
The user is free to modify the dashboard or even add more panels. The user can inject directly data to the Influx database (http://localhost:8086 – change localhost if accessing the database from an external machine). When injecting new data, the new time series can be automatically found, when creating a new panel.
The panel is presented in Figure 6. The panels depict the time variation of:
•	Distance between vehicles.
•	SNR
•	EVM
•	Latency
•	Number of received bits 
•	Throughput
•	Packet Errors
•	PER
•	Bit Errors
•	Bit Error Rate.

