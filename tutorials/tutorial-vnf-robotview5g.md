<!-- TITLE: Tutorial Vnf implementation -->
<!-- SUBTITLE: VNF implementation tutorial based on experience of an Open Call 1 participant -->

# VNF implementation tutorial based on experience of an Open Call participant

This is a tutorial for creation of a Virtual Network Function instance. It is based on experience of an Open Call 1 participant, Netictech, performing an experiment RobotView5G. Its purpose is to provide  additional  support  to  future  open  call  participants  in  order  for  them  to  ease  the development and deployment of their VNF machines within 5GinFIRE infrastructure. 


## Terminology 
The actors in 5GinFIRE have been classified into the following roles

●  Experimenter: This role represents the user that will utilize 5GinFIRE’s services and tools to deploy an experiment. 
●  VxF/VNF developer: This role is responsible for: 
○  developing a VNF machine, 
○  creating a VNFD and uploading it to the 5GinFIRE portal, 
○  creating a NSD and uploading it to the 5GinFIRE portal. 
●  Mentor: a member of the 5GinFIRE consortium acting as a representant and a contact person of the consortium, providing technical assistance for the VNF developer. 
●  Testbed provider: This role represents users that are responsible for testbed administration, configuration, integration, adaptation, support, etc. 
●  Services administrator: This role represents the user that are responsible for maintenance of the 5GinFIRE services. 



##  VNF machine 
VNF (Virtual Network Function) machine is a virtual machine that is intended to be run as a Virtual  Network Function. 

###  Obtaining a scratch machine 

The first step of the process of preparation of a VNF machine is to obtain a scratch machine. A scratch machine is a machine acting as a VNF too, but performing another function. It is easier to adjust this machine to the purpose of the experiment the OC participant is willing to perform than to build the machine from a clean OS cloud image.  For the purpose of our experiment, we used OpenCV Transcoder VNF, prepared and provided by the 5GinFIRE consortium. 

###   Deployment of the scratch machine 
The scratch machine is available as a file  to be deployed. However, deployment of it requires a cloud infrastructure and some know-how. It is easier to have the machine deployed by the 5GinFIRE consortium. In order to be able to do that, an experiment Mentor is to be contacted and asked for deployment of the OpenCV Transcoder VNF machine in one of the 5GinFIRE’s federated infrastructure centers. A VNF developer is then provided with credentials to the machine and credentials with configuration file enabling access to VPN, from where the machine can be reached.

###  Development 
Having gained access to the machine, a VNF developer is able to deploy their software. In our case this involved heavy redesign of our application, as now it was to run headless, without a GUI. The application needs to be adjusted to running from scripts, where important options are exposed as parameters, for an experimenter to provide values of. Furthermore, a way has to be developed for results generated by the VNF to be provided to the experimenter. In our case, there was a script generated that performed upload of generated files using SFTP. What’s more, optional but highly useful was a status channel, providing up-to-date information about the current status of processing on the VNF. For that, we used echo calls piped to netcat


##   VNFD 

After the VNF machine is ready and necessary script parameters are known, a VxF developer is ready  to start creation of a VNF Descriptor (VNFD). The easiest course of action is not to start clean, bo to work on files from an example provided by the 
5GinFIRE consortium. In our case, we used OpenCV Transcoder VNF.  
### Obtaining files to work on 

The first step is to clone the GitHub repository containing the OpenCV Transcoder VNF. Execute the following command in the shell: 
 

```text
$ git clone https://github.com/5GinFIRE/opencv_transcoder_vnf.git 5ginfire-transcoder-vnf-original-image 
```

 
In order to make the process less prone to errors, it is best to edit the .yaml files using a diff editor. In order to be able to do that, it best first to copy the contents of the cloned directory to another location: 
 

```text
$ cp -R 5ginfire-transcoder-vnf-original-image my-vnf-image 
```

After the directory is duplicated, let’s delete the obsolete git files from the new directory: 
 

```text
$ rm -r my-vnf-image/.git 
```

 
Now we are ready to edit the files. 




