<!-- TITLE: Red Zinc Emergency Video -->
<!-- SUBTITLE: A quick summary of Red Zinc Emergency Video -->

# Red Zinc Emergency Video
## Experiment Overview 
The idea of this experiment is to scale test the video stream in a simulated 5G test environment to check if it is feasible to carry out the potential number of required emergency video calls to a satisfactory quality level, in a city with a population of several million people and multiple ambulances. 

**Figure 1 - Paramedic Use Case**

The idea of this experiment is to scale test the video stream in a simulated 5G test environment to check if it is feasible to carry out the potential number of required emergency video calls to a satisfactory quality level, in a city such as Madrid. Madrid has a population of several million people and multiple ambulances. 
Our hypothesis is each ambulance currently has an average of 1 event per hour.  With the advent of on-demand 5G wearable video technology, there is a potential of each event converting to a ten minute video call or a total potential of thousands of video minutes per day.
The method of the experiment is:
*	Use the 5GinFire testbed to simulate and test virtualized video cameras. 
*	Stress test the necessary requisite video streaming functions of security, application stack, group video and QoS functions in an Open Source Mano VNF environment.
*	Assess the feasibility of this volume of video streams and determine the requisite number of CPU and VM to support this target.

## Experiment Display
To carry out the tests planed, the following structure, Figure 2 is the configuration used to capture the results. 

**Figure 2 - Experiment Deployment**


The VxFs created to display the experiment are the following.

### VxF

**Pulse Generator VNF**

This VNF will generate random video to send to the WebRTC Server using as source an mp4 video file.


```yaml
vnfd:vnfd-catalog:
    vnfd:
    -   id: rz_pulseGenerator_vnfd
        name: rz_pulseGenerator_vnfd
        short-name: rz_pulseGenerator_vnfd
        description: RedZinc Pulse Generator VNF 
        vendor: RedZinc
        version: '1.0'

        connection-point:
        -   id: vnf-mgmt
            name: vnf-mgmt
            short-name: vnf-mgmt
            type: VPORT
        -   id: vnf-data
            name: vnf-data
            short-name: vnf-data
            type: VPORT
            port-security-enabled: false

        mgmt-interface:
            cp: vnf-mgmt

        # Place the logo as png in icons directory and provide the name here
        logo: redzincLogo.png

        # Atleast one VDU need to be specified
        vdu:
        -   id: pulseGenerator_vdu
            name: pulseGenerator_vdu
            description: pulseGenerator_vdu
            count: '1'
            image: rz_pulseGenerator
            vm-flavor:
                vcpu-count: '1'
                memory-mb: '6342'
                storage-gb: '21'
            interface:
            -   name: eth0
                position: '1'
                type: EXTERNAL
                virtual-interface:
                    type: VIRTIO
                external-connection-point-ref: vnf-mgmt
            -   name: eth1
                position: '2'
                type: EXTERNAL
                virtual-interface:
                    type: VIRTIO
                external-connection-point-ref: vnf-data

```


**WebRTC/HotDesk Server VNF**

This VNF will receive the video in the WebRTC server and along with the HotDesk will allow the user to visualize the video in the browser.


```yaml
vnfd:vnfd-catalog:
    vnfd:
    -   id: rz_janus_hotdesk_vnfd
        name: rz_janus_hotdesk_vnfd
        short-name: rz_janus_hotdesk_vnfd
        description: RedZinc Janus and Hotdesk VNF 
        vendor: RedZinc
        version: '1.0'

        connection-point:
        -   id: vnf-mgmt
            name: vnf-mgmt
            short-name: vnf-mgmt
            type: VPORT
        -   id: vnf-data
            name: vnf-data
            short-name: vnf-data
            type: VPORT
            port-security-enabled: false

        mgmt-interface:
            cp: vnf-mgmt

        # Place the logo as png in icons directory and provide the name here
        logo: redzincLogo.png

        # Atleast one VDU need to be specified
        vdu:
        -   id: janus_hotdesk_vdu
            name: janus_hotdesk_vdu
            description: janus_hotdesk_vdu
            count: '1'
            image: rz_janusHotdesk
            vm-flavor:
                vcpu-count: '1'
                memory-mb: '6342'
                storage-gb: '21'
            interface:
            -   name: eth0
                position: '1'
                type: EXTERNAL
                virtual-interface:
                    type: VIRTIO
                external-connection-point-ref: vnf-mgmt
            -   name: eth1
                position: '2'
                type: EXTERNAL
                virtual-interface:
                    type: VIRTIO
                external-connection-point-ref: vnf-data

```

The NSDs created to display the experiment are the following.

### NSD

The NSDs used to display the experiment are the following

**Experiment NSD**


```yaml
nsd:nsd-catalog:
    nsd:
    -   id: rz_experiment_nsd
        name: rz_experiment_nsd
        short-name: rz_experiment_nsd
        description: Full experiment test 
        vendor: RedZinc
        version: '1.0'

        # Place the logo as png in icons directory and provide the name here
        logo: redzincLogo.png

        # Specify the VNFDs that are part of this NSD
        constituent-vnfd:
        -   vnfd-id-ref:  rz_pulseGenerator_vnfd
            member-vnf-index: '1'
        -   vnfd-id-ref: rz_janus_hotdesk_vnfd
            member-vnf-index: '2'
               
        vld:
        -   id: mgmtnet
            name: mgmtnet
            short-name: mgmtnet
            type: ELAN
            mgmt-network: 'true'
            vim-network-name: provider
            vnfd-connection-point-ref:
            -   vnfd-id-ref:  rz_pulseGenerator_vnfd
                member-vnf-index-ref: '1'
                vnfd-connection-point-ref: vnf-mgmt
            -   vnfd-id-ref: rz_janus_hotdesk_vnfd
                member-vnf-index-ref: '2'
                vnfd-connection-point-ref: vnf-mgmt
        -   id: datanet
            name: datanet
            short-name: datanet
            type: ELAN
            vim-network-name: provider2
            vnfd-connection-point-ref:
            -   vnfd-id-ref: rz_pulseGenerator_vnfd
                member-vnf-index-ref: '1'
                vnfd-connection-point-ref: vnf-data
            -   vnfd-id-ref: rz_janus_hotdesk_vnfd
                member-vnf-index-ref: '2'
                vnfd-connection-point-ref: vnf-data
```


**Experiment NSD with static IPs**

```yaml
    nsd:nsd-catalog:
        nsd:
        -   id: rz_experiment_madrid_nsd
            name: rz_experiment_madrid_nsd
            short-name: rz_experiment_madrid_nsd
            description: Full experiment test in Madrid
            vendor: RedZinc
            version: '1.0'

            # Place the logo as png in icons directory and provide the name here
            logo: redzincLogo.png

            # Specify the VNFDs that are part of this NSD
            constituent-vnfd:
            -   vnfd-id-ref:  rz_pulseGenerator_vnfd
                member-vnf-index: '1'
            -   vnfd-id-ref: rz_janus_hotdesk_vnfd
                member-vnf-index: '2'
                   
            vld:
            -   id: mgmtnet
                name: mgmtnet
                short-name: mgmtnet
                type: ELAN
                mgmt-network: 'true'
                vim-network-name: provider
                vnfd-connection-point-ref:
                -   vnfd-id-ref:  rz_pulseGenerator_vnfd
                    member-vnf-index-ref: '1'
                    vnfd-connection-point-ref: vnf-mgmt
                -   vnfd-id-ref: rz_janus_hotdesk_vnfd
                    member-vnf-index-ref: '2'
                    vnfd-connection-point-ref: vnf-mgmt
            -   id: datanet
                name: datanet
                short-name: datanet
                type: ELAN
                vim-network-name: provider2
                vnfd-connection-point-ref:
                -   vnfd-id-ref: rz_pulseGenerator_vnfd
                    member-vnf-index-ref: '1'
                    ip-address: 10.4.32.241
                    vnfd-connection-point-ref: vnf-data
                -   vnfd-id-ref: rz_janus_hotdesk_vnfd
                    member-vnf-index-ref: '2'
                    ip-address: 10.4.32.242
                    vnfd-connection-point-ref: vnf-data
```


## Results

