<!-- TITLE: MARATHON (MAnagement of Radio Access neTwork slicing witH multi-applicatiON concurrency)  -->
<!-- SUBTITLE: A quick Tutorial how to compile and start the MARATHON VNFs and Radio Slice Management Function experiment over the WINS_5G testbed using HyDRA -->

# MARATHON description

The MARATHON experiment designed and developed a Radio Slicing Management Function (RSMF) that extends the WINS_5G platform with radio slicing management capabilities.
It was conceived to investigate the practical implementation and management of network slicing in 5G systems.  

The implementation of the MARATHON experiment consists of five Virtual Network Functions (VNFs) and four USRPs, as illustrated the figure. The VNFs are implemented using Linux Virtual Machines (VM).

![Marathon architecture](/uploads/marathon/Marathon-components.png)

### **Organization:** 
Universitat Politecnica de Catalunya (UPC)
[https://www.upc.edu](https://www.upc.edu)


# MARATHON VNFs from GitHub

The MARATHON VNFs are available at GitHub: https://github.com/annaumbert/Marathon/tree/master/vnfd_ansible

## build

The build script creates all the following VNFD and NS files:

1. marathon_hydra_server_vnfd.tar.gz
2. marathon_vbs_vnfd.tar.gz
3. marathon_vue1_vnfd.tar.gz
4. marathon_vue2_vnfd.tar.gz
5. marathon_vue3_vnfd.tar.gz
6. marathon_ns.tar.gz

File 6 (marathon_ns.tar.gz) is the basic experiment consisting of a 1 Base Station, 1 Hydra Server and 3 User Equipments.

To execute the build script (in case you make modifications) just type:

```
./build
```

## osm

The osm script is a simple utilitaria to easen the task of installing, uninstalling, creating, and deleting VNFDs and NSDs.
Type the following command to get a detailed usage of it:
```
./osm l
```
To run the experiment type:
```
./osm i
```

The IP addresses configured at slice.py are:

vbs_ip="192.168.5.94"

vue1_ip="192.168.5.95"

vue2_ip="192.168.5.96"

vue3_ip="192.168.5.100"

If the assigned VM IPs are different you must kill the rsmf process, change the IPs and run again with:
```
sudo python rsmf.py
```


### Testing

- Connect to "marathon_vbs" (Virtual VBS VNF), "marathon_vue1" (Virtual VUE#1 VNF), "marathon_vue2" (Virtual VUE#2 VNF) and "marathon_vue3" (Virtual VUE#3 VNF) VMs using ssh.
- In "marathon_vbs" open a browser (Firefox is already installed) and type (with the vbs_ip):
```
192.168.5.94:8888
```
The RSMF main page should appear in the browser, and you can start configuring Slices.

- From "marathon_vbs" ping  the tap interfaces of "marathon_vue1" with IPs 1.1.1.2, and "marathon_vue2" with IPs 2.2.2.2, and "marathon_vue3" with IPs 2.2.2.3.
```
ping 1.1.1.2
```
or
```
ping 2.2.2.2
```
or
```
ping 2.2.2.3
```

- From "marathon_vue1" ping the tap0 interface of "vbs", IP 1.1.1.1
```
ping 1.1.1.1
```

- From "marathon_vue2" or "marathon_vue3" ping the tap1 interface of "vbs", IP 2.2.2.1
```
ping 2.2.2.1
```

## Troubleshooting

### **Execution problems**
Access each VM and kill the python process.


* In machine "hydra-server" execute (replace 192.168.5.92 by the ip of iris2):
```
python ~/gr-hydra/grc_blocks/app/ansible_hydra_gr_server.py --ansibleIPPort 192.168.5.92:5000
```

* In machine "marathon_vbs" execute (replace 192.168.5.94 by the IP of iris2):
```
python ~/gr-hydra/grc_blocks/app/ansible_hydra_gr_client_2tx_2rx.py --ansibleIP 192.168.5.94
```

* In machine "marathon_vue1" execute:
```
python ~/gr-hydra/grc_blocks/app/ansible_hydra_vr1_rx.py
```

* In machine "marathon_vue2" and "marathon_vue3"  execute:
```
python ~/gr-hydra/grc_blocks/app/ansible_hydra_vr2_rx.py
```

### **Connectivity problems**
If all processes run correctly, but there is no ping connectivity, a tuning of the gain (mul & mul2) is required. This is due to the USRPs assigned are not always the same, and they have different locations. 
A guide to do this can be requested to the Iris Testbed Manager (wireless.testbed@connectcentre.ie).
