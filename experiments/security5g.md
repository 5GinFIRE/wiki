<!-- TITLE: ExSEC - Security 5G Experiment Example-->
<!-- SUBTITLE: Security 5G Experiment Example -->

# ExSEC - Security 5G Experiment Example

Our use case to validate the developed technology is an assured-security IoT/media application-service from the healthcare vertical domain (remote patient monitoring). The patients’ vital health parameters (e.g., heart rate) as well as video stream of different health exercises are securely collected on the patients’ smart phones (emulated user equipment (UE) provided by 5G Media Vertical testbed), - stored and - given access to through a set of secured APIs. The component that implements this functionality is a micro-service called patient service, composed of the data collector and the patient data application functions. It provides a web GUI service to the end-user client system (web browser) to access vital health parameters and surveillance video of patients and see aggregated information thereof, according to the access-rights attached to their specific user-role (patient, clinician, general practitioner, etc.). The integration of the (emulated) user equipment and 5G connectivity via Open5GCore as well as the utilization of the video streaming capabilities emphasize the domain specifics offered by the TNO 5G Media Vertical testbed.

In our example scenario we assume that some medical work with eHealth devices is taking place and we want to process their video streams and save as video files. We use Edge Cloud to collect high resoultion video stream and process it before uploading to Core Cloud where it is written to a file and additional information about the video is collected.

![Use Case](/uploads/secure_edge.png "Use Case Scenario Secure from the Edge")

## Components description

### Video Aggregation - vaf_vnfd
This VNF is part of TNO Media Vertical testbed. For more information please refer to the TNO Media Vertical testbed description.

### Application Proxy - apf_vnfd
The application proxy function is responsible to proxy all traffic from well behaved clients to the object storage function containing the real data and all traffic from malicious clients to the object storage function containing the fake data (honeypot).

### Object Storage - osf_vnfd
The object storage function is responsible to provide a S3 compatible storage backend.

# Recreating the experiment.
0. Request setup of the emulated user equipment and 5G components from TNO.
1. Request deployment of vaf_osf_nsd from 5GinFIRE portal at the TNO Media Vertical testbed.
2. After successful deployment you should be able to store video streams from the user equipment via VAF GUI.
3. To test the security enhanced feature, you can simulate an attack against the APF and should be denied access to the recorded video stream.


