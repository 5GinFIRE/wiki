![5 Ginfire Logo 3](/uploads/5-ginfire-logo-3.png "5 Ginfire Logo 3")<!-- TITLE: Icarus -->
<!-- SUBTITLE: A quick summary of Icarus -->

# Description of the VNF

ICARUS experiment is executed on the PPDR ONE testbed utilizing data from qMON network sensors acting as IoT data emulators and having as mentor Mr Luka Koršič  on behalf of PPDR ONE facility.

ICARUS plans to combine the agility of a virtual Deep Packet Inspection (vDPI) function with the flexibility of data protocol mapping functions (e.g. CoAP, MQTT, HTTP to generic UDP), allowing to the overlay IoT services to be automatically deployed and programmed by a single domain coordinator/orchestrator/experimenter. 

Our main objective of the proposed SDN/NFV-enabled IoT ICARUS experiment is to expand the interoperability level of the 5GINFIRE/PPDR ONE by providing an agile manner for data interoperability.

![Architecture](/uploads/ppdrone/icarus_architecture_final.png "Architecture"){.align-center}
# Running an experiment
The VMs from PPDR ONE are used to generate IoT data for the ICARUS experiment. 
* qMON Agent : This is the probe actually doing the measurements and providing the data in XML format. This can scaled up to more qMON Agents in order to have larger amount of IoT data available, as already depicted in the topology of Figure 9.
* qMON Server: This is the measurement endpoint for qMON Agents, it can serve many qMON Agents at the same time.
* qMON Collector: This is where the XMLs with the results are uploaded and every one minute are parsed in order for them to be saved to the qMON database. In this VM, INFOLYSiS custom code is being executed in order to extract the data from the XMLs (before being saved to the qMON database). The extracted data are encapsulated in one of the three protocols that are part of ICARUS experiment (HTTP, CoAP, MQTT) and then they are transmitted to INFOLYSiS IoT Proxy.


The VNFs setup by INFOLYSiS were used for executing the experiment and there are 6 VNFs in total :

•	INFOLYSIS IoT Proxy: First entry point of IoT data to INFOLYSiS system. The data can be of different protocols (HTTP, CoAP, MQTT) and they are directly forwarded to INFOLYSiS vDPI. (management IP: 10.154.97.53)
•	INFOLYSIS vDPI: A DPI VNF that can detect different IoT protocol flows and redirect them to the appropriate mapping VNF (e.g. HTTP to UDP, CoAP to UDP, MQTT to  UDP)
•	In the vDPI VNF, INFOLYSiS will also design, develop and run a SDN-app, using REST, suitable for providing in an automatic way IoT interoperability for the 3 different IoT data protocols (CoAP, MQTT, HTTP). (management IP: 10.154.97.79)

•	INFOLYSIS HTTP Mapping VNF: Mapping of HTTP IoT data to plain UDP, providing interoperability. (management IP: 10.154.97.81)
•	INFOLYSIS CoAP Mapping VNF: Mapping of CoAP IoT data to plain UDP, providing interoperability. (management IP: 10.154.97.54)
•	INFOLYSIS MQTT Mapping VNF: Mapping of MQTT IoT data to plain UDP, providing interoperability. (management IP: 10.154.97.83)
•	INFOLYSIS IoT vGW: Final destination of interoperable IoT data on the INFOLYSiS system. A web GUI is provided in order for the user to be able to monitor the data in real time and also ensure that all VNFs are in operation and data are fetched properly by the PPDR ONE. Also, the interoperable IoT data output is saved in a MySQL database, which can be used for future extensions and integrations via the provided API. (management IP: 10.154.97.55)
Each VNF has the following resources:
•	Memory: 2048 MB
•	Storage: 20GB
•	VCpu : 1


# Example Results

asd



