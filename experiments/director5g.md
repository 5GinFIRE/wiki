<!-- TITLE: Director 5G DynamIc REsource instantiation and ConTrol for 5G content delivery netwORks -->
<!-- SUBTITLE: A quick Tutorial how to compile and start the Director 5G VNF and Named data networking experiment over the HyDRA radio slice -->

# download DIRECTOR 5G VNFs from github
git clone https://github.com/jerryocoileain/director5g-vnfd.git
# build 

The build script creates all the following VNFD and NS files:

ndn_hydra_client_2tx_2rx_vnfd.tar.gz
ndn_hydra_server_vnfd.tar.gz
ndn_hydra_client_rx2_vnfd.tar.gz
ndn_hydra_client_rx2_vnfd.tar.gz
ndn_hydra_basic_nsd.tar.gz
ndn_cdn_server_vnf.tar.gz

File 5 is a basic experiment consisting of a 2-EVI Base Station (ndn_hydra_client_2tx_rx and ndn_hydra_server), 2 clients (rx1 and rx3, for the 1st and 2nd EVI, respectively), and the ndn_cdn server hosting NDN data.

To execute the build script just type:

```text
./build
```

# osm	
	
The osm script is a simple utilitaria to easen the task of installing, uninstalling, creating, and deleting VNDFs and NSDs. Type the following command to get a detailed usage of it:


```text
./osm l
```

# basic_nsd

This scenario represents a case with 2 slices (in this case we dont have any performance difference between them).

The scenario is as follows:

![Ndn Hydra Slice Ping Scheme](/uploads/director-5-g/ndn-hydra-slice-ping-scheme.png "Ndn Hydra Slice Ping Scheme")
# Testing

From "ndn_hydra_client_2tx_2rx" ping the tap interfaces of "ndn_hydra_rx1" and "ndn_hydra_rx2" with IPs 1.1.1.2 and 2.2.2.2, respectively.
ping 1.1.1.2
or

```text
ping 2.2.2.2
From "ndn_hydra_rx1" ping the tap0 interface of ""ndn_hydra_client_2tx_2rx", IP 1.1.1.1
ping 1.1.1.1
From "ndn_hydra_rx2" ping the tap1 interface of ""ndn_hydra_client_2tx_2rx", IP 2.2.2.1
ping 2.2.2.1
```


# Experimenting with Named Data Networking over HyDRA radio slices.

In machine "ndn_hydra_rx1" exec:

```text
python ~/gr-hydra/grc_blocks/app/ansible_hydra_vr1_rx.py
```

In machine "ndn_hydra_rx1" exec (will request aideens_dolmen segments 000-057 x 30times):

```text
for ii in {000..030}; do for i in {000..057}; do ndncatchunks ndn:/dp/data/cc/aideens_dolmen_19_$i >file.ou ; done;echo "-------------------------";echo $ii;echo "-------------------------"; done
```

In machine "ndn_hydra_rx2" exec:

```text
python ~/gr-hydra/grc_blocks/app/ansible_hydra_vr2_rx.py
```

In machine "ndn_hydra_rx2" exec (will request aideens_dolmen segments 000-057 x 30times):

```text
for ii in {000..030}; do for i in {000..057}; do ndncatchunks ndn:/dp/data/cc/aideens_dolmen_19_$i >file.ou ; done;echo "-------------------------";echo $ii;echo "-------------------------"; done
```


# Troubleshooting
Access each VM and kill the python process.

In machine "ndn_hydra-server" exec (replace 192.168.5.54 by the ip of iris2):

```text
python ~/gr-hydra/grc_blocks/app/ansible_hydra_gr_server.py --ansibleIPPort 192.168.5.54:5000
```

In machine "ndn_hydra_client_2tx_2rx" exec (replace 192.168.5.82 by the IP of iris2):

```text
python ~/gr-hydra/grc_blocks/app/ansible_hydra_gr_client_2tx_2rx.py --ansibleIP 192.168.5.82
```
