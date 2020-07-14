<!-- TITLE: Robotview 5G -->
<!-- SUBTITLE: Smart City Robotic Surveilance Platform -->

![5 Ginfire Logo](/uploads/5-ginfire-logo.png "5 Ginfire Logo")
# Robotview 5G - Smart City Robotic Surveilance Platform
**In 2018, NETICTECH was conducting research work related to real-time transmission of video feeds via wireless networks. The experiments were funded by the Horizon 2020 EU programme. The research focused on the video quality and transmission technology, and it did not include facial recognition or personal data processing capabilities. There has been no follow-up to this work, which concluded in 2018 and has not proceeded beyond the simulation stage. No personal data was processed or gathered during the experiment.**

![Robotview Robot Pic](/uploads/robot-view-5-g/robotview-robot-pic.jpg "Robotview Robot Pic")

**Organization**
Cybernetic Technologies NETICTECH S.A. - http://netictech.com/ 

**Experiment description**
NETICTECH is developing the Wireless Robotic Surveillance Platform “RobotView”, enabling real-time video surveillance using WiFi and mobile networks from remote controlled robots, drones and other video-monitoring devices. The platform is designed for police, security and rescue forces to enable surveillance and operation in places hazardous to humans. In times of heightened security risks, RobotView can save the lives of officers by allowing them to send a robot or drone and remotely watch the video from the installed cameras in real-time. RobotView is not bound to a specific solution, it is a vendor-agnostic system, allowing to transmit video coming from cameras installed on robots, drones or vehicles using wireless networks. The system has adaptation mechanism to react to changing network parameters and maintain the needed video quality. This is done using automatic video quality analysis tools that, based on no-reference QoE measurement algorithms, provide a set of QoE parameters in real-time. The system is in the testing stage now and in the experiment we wanted to test our encoding and transmission parameters in  order to tune them for usage in various video-monitoring scenarios. Figure 1 shows the general architecture of the RobotView platform. 

![Robotview Pic 2](/uploads/robot-view-5-g/robotview-pic-2.jpg "Robotview Pic 2")

**General architecture of the RobotView platform**
The heart of the RobotView platform is the Adaptive Video Processor (AVP). The AVP is capable of changing the encoding and transmission parameters of the video. The Controller of the AVP receives QoE-related information, the status of the network link, availability of local resources for video encoding and user input from the Security Application. Based on this data it decides which encoding and transmission profiles the AVP should use to enhance the QoE, and thus the usability of the surveillance video, in the changing conditions of wireless networks. In our current implementation, the central Security Application is providing QoE video analysis capacities, however taking advantage of the 5G technology we want to distribute the QoE video analysis to the edge nodes. We have tested this within our experiment.

The system is in the testing stage now and in the experiment we have focused on testing our encoding and transmission parameters in order to tune them for usage in various video-monitoring scenarios. For our experiment we have used the Smart City Safety testbed and took advantage of the Raspberry Pi computers. In the experiment our goals was to deploy our no-reference QoE analysis tool as a VNF, so we could utilize the capacities of edge computing and perform QoE analysis close to the video source. 
In the experiments, we have used the test procedure described below. We have determined how changing the value of a single video parameter can affect the output results.
•	The following video parameters have been tested:
o	FPS {5, 15, 30, 60}
o	bitrate = {31, 62, 125, 250, 500, 1000, 2000, 4000, 8000 [kbps]}
o	resolution = {640x360, 960x540, 1280x720, 1920x1080}
•	The clips have been scaled up to 1920x1080 before being displayed and measured.
•	For each combination of the tested parameter values, video clips have ben uploaded to the Raspberry Pi computers and streamed over the wireless network
•	Our QoE Measurement Tool VNF, deployed on the MEC, has performed the QoE analysis
•	Network performance parameters have been measured: delay, jitter, packet loss, delay under load, packet loss under load.
•	Video clips have been displayed for visual analysis by a tester, to verify the quality based on human perception.
•	The results have been analysed, correlating the automatic QoE measurement, the subjective QoE perception and the QoS measurements, to define the encoding and transmission parameters for given values of parameters.

**Results**
During our experiments, we have tested our current video encoding and transmission profiles. We have studied how changing various QoS parameters influences the QoE for surveillance video and, based on that, tune our encoding and transmission profiles. For this purpose, we have used two metrics as initial parameters that determine further actions: Temporal Activity and Spatial Activity. Temporal Activity provides information about the motion in an image. Spatial Activity, on the other hand, determines the amount of image details. By analysing both metrics simultaneously at a constant rate, we have monitored processes on the video and thus constantly evaluate whether there is movement in the screen or not. If the level of motion and visual details in the video increases, values of the above mentioned QoE metrics should increase as well. By monitoring the visual activity in the camera stream, it was possible to take advantage of other QoE metrics, in order to maintain the highest possible quality of the video for a given situation. Values of the most important QoE metrics enable the transcoder to adjust the bitrate, FPS value or resolution of a video. From a variety of statistics gathered for the transcoder, three of the most important ones are Block Loss, Freezing and Blockiness.

![Robotview Pic 3](/uploads/robot-view-5-g/robotview-pic-3.jpg "Robotview Pic 3")
Spatial activity, no side traffic
![Robotview Pic 4](/uploads/robot-view-5-g/robotview-pic-4.jpg "Robotview Pic 4")
Spatial activity, side traffic present
![Robotview Pic 5](/uploads/robot-view-5-g/robotview-pic-5.jpg "Robotview Pic 5")
Temporal activity, no side traffic
![Robotview Pic 6](/uploads/robot-view-5-g/robotview-pic-6.jpg "Robotview Pic 6")
Temporal activity, side traffic present

The spatial activity for low bitrate streams is significantly lower for intra refresh 1s GOP streams, in comparison to the other streams. At high bitrate streams, there is a slight advantage of the spatial activity for the intra refresh 4s GOP streams. Spatial activity results were very close between the GOP variants. There is a slight disadvantage for IDR streams in HD resolutions. This type of GOP performs slightly better only in SD and low resolutions when allocated little bandwidth.
The temporal activity is more less at the same level for all GOP types. In some configurations, temporal activity is the lowest with GOP with intra refreshed sequences with short periods. The other two GOP types are very close, with no clear winner between them. The side traffic causes a slight decrease in the temporal activity values.

**Conclusions**
We have tested correlations between QoS and QoE, as well as defined encoding and transmission profiles for various surveillance scenarios. We have also tested how edge node processing can decrease the overall bandwidth usage for surveillance videos. Having the media routing on an edge node allows to disable the sending of the other videos to the monitoring application, and only to the storage, which can be located in a different location or even also distributed on the edge nodes. Taking this into account and assuming a bit rate of 4 Mbps for a Full HD video stream, introducing 5G architecture could lower the used bandwidth from 80 Mbps to 17 Mbps. Finally, we have added a new VNF to the 5GinFIRE catalogue that is be available for other experimenters, providing real-time QoE measurement for video streams.