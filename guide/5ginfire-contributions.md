<!-- TITLE: Best Current Practices and Lessons Learned -->
<!-- SUBTITLE: A summary of Best Current Practices and Lessons Learned regarding Management and Orchestration (MANO) -->

# Multi-site deployments with OSM Release FIVE
The following text describes a procedure to enable the multi-site deployment of network services in OSM Release FIVE, avoiding the utilization of a Wan Infrastructure Manager (WIM). This procedure has been developed in the context of the  [H2020 5GinFIRE project](https://5ginfire.eu).

## 1. WIM account creation 

Create a “dummy WIM” account in the OSM, specifying which VIMs will be used for multi-site deployments. This step is needed because in this type of deployments the RO first verifies the existence of a WIM account with the information about all the involved VIMs; and if it does not exist, the deployment is rejected. This information is defined by the OSM information model and can be provided in a JSON file at the WIM account creation time.

The following OSM-CLI command shows the creation of a WIM account, and uses a specific JSON file that is also provided as an example ([dummy_wim_port_mappings.json](/uploads/contributions-osm-r-5-multi-site/dummy-wim-port-mappings.json "Dummy Wim Port Mappings")):


```text
osm wim-create --name dummy-wim --wim_type odl --url http://dummy-wim-odl --user osm --password osm --wim_port_mapping dummy_wim_port_mappings.json
```


Regarding the parameters of the OSM-CLI command, and the JSON file, we want to note that:

- This procedure considers ODL WIM types. Hence, the value of the “--wim_type” parameter is set to “odl”. 

- The JSON file includes the specification of two VIMs ("openstack-site-1" and "openstack-site-2”). Additional VIMs can be specified in the file, depending on the specific configuration that needs to be done.

- The VIM names specified in the JSON file must match the names of the VIMs attached to OSM.

- The rest of the information included in the JSON file (i.e., “pop_switch_port”, “pop_switch_dpid” and “wan_service_mapping_info”) is required because of the OSM information model but never checked. 

## 2. Update RO source-code 
Modify the source code of the OSM Resource Orchestrator (RO). In multi-site deployments, the RO communicates with the WIM in order to create/check data paths among the involved sites, and waits for a positive answer from the WIM to proceed with the deployment. To bypass this behavior, replace the source code of the file “/usr/lib/python2.7/dist-packages/osm_ro/wim/wimconn_odl.py”, which is inside the RO container, with the source code provided by the file [modified_wimconn_odl.py](/uploads/contributions-osm-r-5-multi-site/modified-wimconn-odl.py "Modified Wimconn Odl")

Execute the following commands to do the replacement:


```text
ro_container_id=$(docker ps | grep opensourcemano/ro:latest | cut -d" " -f1)
docker cp modified_wimconn_odl.py $ro_container_id:/usr/lib/python2.7/dist-packages/osm_ro/wim/wimconn_odl.py
```


## 3. Save changes
Make the changes persistent. For this purpose, the image of the RO container needs to be updated, in order to let OSM start the updated RO after every OSM machine restart. This is done through the following commands, which get the ID of the opensourcemano/ro container and commit the change of the new image:


```text
ro_container_id=$(docker ps | grep opensourcemano/ro:latest | cut -d" " -f1)
docker commit $ro_container_id opensourcemano/ro:latest
```
