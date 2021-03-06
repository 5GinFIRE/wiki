![5 Ginfire Logo 3](/uploads/5-ginfire-logo-3.png "5 Ginfire Logo 3")<!-- TITLE: Icarus -->
<!-- SUBTITLE: A quick summary of Icarus -->

# Description of the experiment

ICARUS experiment is executed on the PPDR ONE testbed utilizing data from qMON network sensors acting as IoT data emulators and having as mentor Mr Luka Koršič  on behalf of PPDR ONE facility.

ICARUS plans to combine the agility of a virtual Deep Packet Inspection (vDPI) function with the flexibility of data protocol mapping functions (e.g. CoAP, MQTT, HTTP to generic UDP), allowing to the overlay IoT services to be automatically deployed and programmed by a single domain coordinator/orchestrator/experimenter. 

Our main objective of the proposed SDN/NFV-enabled IoT ICARUS experiment is to expand the interoperability level of the 5GINFIRE/PPDR ONE by providing an agile manner for data interoperability.

![Architecture](/uploads/ppdrone/icarus_architecture_final.png "Architecture"){.align-center}


The VMs from PPDR ONE are used to generate IoT data for the ICARUS experiment. 
* qMON Agent : This is the probe actually doing the measurements and providing the data in XML format. This can scaled up to more qMON Agents in order to have larger amount of IoT data available, as already depicted in the topology of Figure 9.
* qMON Server: This is the measurement endpoint for qMON Agents, it can serve many qMON Agents at the same time.
* qMON Collector: This is where the XMLs with the results are uploaded and every one minute are parsed in order for them to be saved to the qMON database. In this VM, INFOLYSiS custom code is being executed in order to extract the data from the XMLs (before being saved to the qMON database). The extracted data are encapsulated in one of the three protocols that are part of ICARUS experiment (HTTP, CoAP, MQTT) and then they are transmitted to INFOLYSiS IoT Proxy.


The VNFs setup by INFOLYSiS were used for executing the experiment and there are 6 VNFs in total :

* INFOLYSIS IoT Proxy: First entry point of IoT data to INFOLYSiS system. The data can be of different protocols (HTTP, CoAP, MQTT) and they are directly forwarded to INFOLYSiS vDPI. (management IP: 10.154.97.53)
* INFOLYSIS vDPI: A DPI VNF that can detect different IoT protocol flows and redirect them to the appropriate mapping VNF (e.g. HTTP to UDP, CoAP to UDP, MQTT to  UDP)
* In the vDPI VNF, INFOLYSiS will also design, develop and run a SDN-app, using REST, suitable for providing in an automatic way IoT interoperability for the 3 different IoT data protocols (CoAP, MQTT, HTTP). (management IP: 10.154.97.79)

* INFOLYSIS HTTP Mapping VNF: Mapping of HTTP IoT data to plain UDP, providing interoperability. (management IP: 10.154.97.81)
* INFOLYSIS CoAP Mapping VNF: Mapping of CoAP IoT data to plain UDP, providing interoperability. (management IP: 10.154.97.54)
* INFOLYSIS MQTT Mapping VNF: Mapping of MQTT IoT data to plain UDP, providing interoperability. (management IP: 10.154.97.83)
* INFOLYSIS IoT vGW: Final destination of interoperable IoT data on the INFOLYSiS system. A web GUI is provided in order for the user to be able to monitor the data in real time and also ensure that all VNFs are in operation and data are fetched properly by the PPDR ONE. Also, the interoperable IoT data output is saved in a MySQL database, which can be used for future extensions and integrations via the provided API. (management IP: 10.154.97.55)

Each VNF has the following resources:
* Memory: 2048 MB
* Storage: 20GB
* VCpu : 1
# Running the experiment

After successfully deploying the experiment in PPDRONE stasionary testbed the steps to verify that it is running are:

1. Login to 5GinFIRE OpenVPN
2. SSH to 5GinFIRE Jump Machine
3. SSH to each VNF from the Jump Machine using username:ubuntu, password:qoe
4. Verify that everything is running

INFOLYSIS IoT Proxy
```text
nohup python /home/ubuntu/server-coap.py &
nohup python /home/ubuntu/server-mqtt.py &
nohup python /home/ubuntu/test-server.py &
```

INFOLYSIS vDPI
```text
nohup python /home/ubuntu/mserver.py &
nohup python /home/ubuntu/dpi-app.py &
nohup python /home/ubuntu/sdn-app.py &
```

INFOLYSIS HTTP
```text
service apache2 start
```

INFOLYSIS CoAP
```text
nohup python /home/ubuntu/mizukek/server1.py &
```

INFOLYSIS MQTT
```text
nohup python /home/ubuntu/mqtt-server.py &
```

INFOLYSIS IoT vGW
```text
nohup python /home/ubuntu/masterserver2.py &
```

# Experiment Results
The interoperable IoT data are stored in a MySQL database in INFOLYSiS IoT vGW.

Ways of accesing the IoT data:
1. Directly by logging in the database using username: root, password:icarus
2. Through the Dashboard at < IoT GW IP address >/infolysisiot/ by using the password: icarus
3. With the REST API by using HTTP GET: http://< IoT GW IP address >/infolysisiot/api/iot.php?limit=< number of IoT data to fetch >&protocol=< original protocol of data HTTP/COAP/MQTT >&source=< IP address that the data originate from >&type=< type of IoT data >
