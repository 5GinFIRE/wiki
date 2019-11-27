## Working with full charms and OSMv5

5G Media Vertical offers a possibility for deployment of descritptors with full (native) charms. 
This feature will only be supported in OSM 6.0.4 so we use a certain workaround for OSM 5 (see Deliverable 4.2 for the details)

You need to be an experimenter using 5G Media Vertical and contact testbed mentors to obtain a private key to access a local instance of the OSM. This instance has charm development environment configured, as in

http://osm-download.etsi.org/ftp/osm-6.0-six/7th-hackfest/presentations/7th%20OSM%20Hackfest%20-%20Session%206.1%20-%20Introduction%20to%20Proxy%20Charms.pdf

Additionally, `builds` subfolder was created. 

Once logged in to OSM, in the `~/charms` folder there is a `source-me.sh` file which needs to be sourced for the charm builder
to know the locations of the files.

```
export JUJU_REPOSITORY=~/charms
export CHARM_BUILD_DIR=$JUJU_REPOSITORY/builds
export CHARM_INTERFACES_DIR=$JUJU_REPOSITORY/interfaces
export CHARM_LAYERS_DIR=$JUJU_REPOSITORY/layers
```

The folder `~/charms/layers/` contains a proxy charm `deployer-charm` 
which has already been built to `~/charms/builds/deployer`. This charm needs to be placed in 
in the `charms` subfolder of VNFD, next to the experimenter's charm . An example is provided in `~/vnfds/vaf_vnfd` folder.

Note, that the `deployer` must the only charm in experimenter's VNFD in `charms` sections
while the experimenter's charms land as parameters, see the example in `~/vnfds/vaf_vnfd` folder

Next, VNFD (as well as NSD) needs to be packaged and uploaded to OSM. A simple script is provided for the illustration:


```
ubuntu@osm:~$ cat build_and_upload.sh
#!/bin/bash
set -x

CHARM=vaf
VNF=vaf_vnfd
#uncommnent if there is a NSD using this VNFD
#NSD=my_nsd
#uncommnent if there is a NS using this VNFD
#NS=testns
#osm ns-delete ${NS}

source ~/charms/source-me.sh
rm -rf ${CHARM_BUILD_DIR}/${CHARM}
cd ~/charms/layers
charm build ${CHARM}

rm -rf ~/vnfds/${VNF}/charms/${CHARM}/
cp -R ~/charms/builds/${CHARM}/ ~/vnfds/${VNF}/charms/
cp -R ~/charms/builds/deployer/ ~/vnfds/${VNF}/charms/

cd ~/vnfds

rm ~/vnfds/${VNF}.tar.gz

#uncommnent if there is a NSD using this VNFD
#osm nsd-delete ${NSD}
#osm vnfd-delete ${VNF}

~/devops/descriptor-packages/tools/generate_descriptor_pkg.sh -t vnfd -N ${VNF}

#osm upload-package ${VNF}.tar.gz
#osm upload-package ${NSD}.tar.gz

```

In order to deploy, certain parameters need to be provided, by sourcing the appropriate file, see `~/scripts/generic.rc` for example.
The experimenters need to alter at least `NSD_NAME`. Optionally, new juju model can be created which requires changing `MODEL` parameter. 
Finally `~/scripts/deploy.sh` needs to be called to start a deployment. `undeploy.sh` script destroys the deployed model.

```
ubuntu@osm:~/scripts$ cat generic.rc
#!/bin/bash

export LOGIN=ubuntu
export PASSWORD=ubuntu
export VIM=5GINFIRE-tenants
export CLOUD=firecloud-controller
export MODEL=default
export NSD_NAME=e2e_service
export NS_NAME=video_aggregation
```
