<!-- TITLE: Touristeyes -->
<!-- SUBTITLE: Tourist Eyes -->

# Tourist Eyes

## Organization
vEyes
## Experiment Description
Blind people encounter huge difficulties in visiting unknown places, even if they have traditional assistive supports like a white stick and a guide dog, or some Tactile Ground Surface Indicators are installed in the visited places. Moreover, a guide dog may often be not available since it may be not immediately assigned to a blind person abroad, or because the person can be allergic to animals. A possible solution that has been adopted in the last few years is to run a GPS-based application on the smartphone of the blind person; however, this does not give a sufficiently high-precision to guarantee safety for the blind person when he/she moves in the city and might not work outdoor. On top of that, orientation and point-of-interest identification prove to be very difficult operations with the currently available technologies.
The objective of this experiment, named Tourist Eyes, is to provide blind tourists, visiting a smart city, with a framework for supporting their activities. Tourist Eyes is proposed by vEyes [1], a non-profit organization whose mission is to realize assistive technologies for blind people. The main idea is porting the testbed Poseidon 2.0 [2, 3], developed by vEyes and already tested for blind swimmers, over a 5G infrastructure, in order to apply Poseidon 2.0 to more complex scenarios like a smart city.
The following pages list and explain in detail the software components needed to carry out the experiment. Afterwards, the preparation and implementation steps needed to set-up the experiment are shown, distinguishing those carried out remotely (e.g the upload of the descriptors in the 5GinFIRE [4] Portal) from those performed at Millennium Square (Bristol).
The application scenario leverages on the facilities provided by the University of Bristol 5G Testbed. 

## 1	Software components
In this section we provide a brief description of the Tourist Eyes components that have been either used or appropriately designed and implemented for the experiment.
### 1.1	.NET Framework
.NET Framework is a software framework developed by Microsoft that runs primarily on Microsoft Windows. It is required for both the Tourist Eyes Manager and Tourist Eyes Console to function.
### 1.2	SQL Server
Microsoft SQL Server is a relational database management system developed by Microsoft. The database is used by the Tourist Eyes system to manage places and related points of interest, cameras, routes, and all the other parameters needed to track the user. 
### 1.3	Web Services
Web services have been designed to support interoperability between the app in the smartphone and the database. Specifically, they are used by the Tourist Eyes app to retrieve from the SQL Server the list of available places, the relevant points of interest and to change the color of the hat/umbrella used by the user in order to be traced.
### 1.4	Tourist Eyes Manager
The Tourist Eyes Manager (briefly Manager) is the core of the entire system. After the system boot, the Manager listens for connections on a certain port. Through the Tourist Eyes App the user sends a request to the Manager to be tracked and guided to safely arrive at the desired destination. After selecting the starting point and the ending point from the Tourist Eyes app, a connection request is sent to the Manager. The Manager then executes a query in the database to retrieve the appropriate route (for each starting point and ending point there is only one route). Afterwards, the first two cameras of the route begin to send video streams to the Manager, as shown in Figure 1: the tracking algorithm is performed on the first camera, while the second one is ready to welcome the blind user.  

![Figue 1](/uploads/touristeyes/picture-1.png "Figure 1")

This way, while the user leaves the first cam, the second one is already sending its video stream to the Manager, which then “shifts” its tracking algorithm from the video stream of the first camera to the stream of the second one while guaranteeing a soft handover procedure. 
When the blind user leaves the first camera and enters the second one, the first camera stops sending video streams and the third camera simultaneously starts its transmission; in the meantime, the user is being tracked by the second cam. The tracking algorithm searches for a pseudo-spherical object of colour previously chosen by the user through the app; moreover, to reduce interference from external objects of the same colour in the surrounding environment, a greyscale filter is applied to each frame of the video frames, grayscaling everything but a squared area near the user. Another filter can be applied to enhance certain colours, so that the System will be able to recognize the hat more easily. Tracking is carried out by specific guidelines that are set using the Console during the set-up of the experiment. When the user crosses one of these guidelines, the Manager sends a message to the app. This message is decoded by the app and transduced into an acoustic signal. The user, after receiving the acoustic beep, will change direction to re-enter the route. Once at the destination, another acoustic beep is sent to the user, and the Manager starts listening for new connections. The Manager also has a graphical interface that has been used for debugging purposes during the execution of the experiment. 

### 1.5	Tourist Eyes Console
The Virtual Eyes console (briefly Console) is implemented as a C# application. The Console aims at simplifying the set-up process of the experiment by providing an intuitive graphical user interface that enables the creation of new routes and the configuration of the route parameters. Therefore, the console drastically reduces the set-up time of the experiment while enabling the management of the system set-up without directly interacting with the database.

### 1.6	Tourist Eyes App
The Tourist Eyes app is the only software component that each user of the system must have installed on his/her smartphone. Through the app, the user sends a request to the Manager to be tracked and guided to safely arrive at the desired destination. The blind user can easily interact with the application thanks to the support of the smartphone's voice assistant and the Tourist Eyes Training App. From the main screen of the app (shown in Figure 3), select "Places" to display the list of places where the Tourist Eyes system is active, as shown in Figure 4.

![Figue 2](/uploads/touristeyes/picture-2.png "Figure 2")

By selecting, for example, Millennium Square, a list of points of interest is displayed (Figure 5); after selecting the starting point, a second list shows the possible points of arrival. Note that the retrieval of all this information is done via Web Services, which are used by the Tourist Eyes app to retrieve the lists of available places and relevant points of interest from the SQL Server. After selecting the point of departure and arrival, a request is sent to the Manager. Afterwards, the user will only have to click GO! (Figure 6). The Manager will now begin to send messages to the app; these messages will be transduced by the app into acoustic beeps that the user will receive through his/her headphones. When the user arrives at the destination, tracking ends and new connections can be accepted by the Manager.


## 2	Experiment Execution
The execution of the experiment, shown in Fig. 9, consists of two phases.
### PHASE 0: Service Activation by a blind tourist
The blind tourist, assisted by the smartphone voice assistant, activates the Tourist Eye app on his/her smartphone and selects the point of departure and arrival from the list of available places and points of interest (Figure 5). In our case, the user selected Grant Statue as the departure and Coffee Shop as the arrival. Then, the app sends a connection request to the Tourist Eyes Manager, including the point of departure and arrival. Afterwards, the Manager will query the database to retrieve the route that connects the starting point to the destination point (highlighted in red in Figure 10), the modules of the route, and the list of cameras associated with the modules. Finally, the Manager sends a video request to the first and second camera of the route. This concludes the system service activation.


![Figue 3](/uploads/touristeyes/picture-3.png "Figure 3")

![Figue 4](/uploads/touristeyes/picture-4.png "Figure 4")

### PHASE 1: Walk from the Grant Statue (A) to the 640East (B)
The blind tourist walks from the Grant Statue through the path highlighted in Figure 15 to reach the 640 East Coffee Shop. When the user starts walking, the Manager begins to track the user through the first camera of the route (shown in Figure 11). When the user makes a 90° rightwards rotation, the tracking algorithm is shifted from first camera to the second one. The user then continues to walk straight for about ten meters (the tracking is shifted from the second camera to the third one and then from the third one to the fourth one – each camera is approximately 4m away from the others); afterwards the user makes a 90° leftwards rotation (the tracking algorithm is shifted to the fifth camera. After completing this rotation, the user will only need to take a few steps to get to the coffee shop. The reverse path, from the 640East (B) to the Grant Statue (A), has been crossed as well. In both paths, the user has successfully managed to reach their destination. During the experiment, the Manager sends data to the app. The data transmitted is encoded by the app and translated into an acoustic signal, which is received by the user through his/her headphones; the user will then perform a specific movement: if no acoustic signals are received, the user keeps walking straight. The user is able to perform the appropriate movement only if he/she has been appropriately trained, using the Tourist Eyes Training app, to recognize the acoustic signals.

![Figue 5](/uploads/touristeyes/picture-5.png "Figure 5")

## 3	Measurements
During the experiment, we identified three different working regions, as reported in the following:
1)	Time Interval P1: Tuesday, October 22nd, from 10:45 to 11.55 am
2)	Time Interval P2: Wednesday, October 23rd, from 12:45 to 1.45 pm 
3)	Time Interval P3: Wednesday, October 23rd, from 4:45 to 5.45 pm 
Specifically:
•	in P1 the system experienced high quality performance, since the video was flowing smoothly through the system.
•	in P2 the system performed poorly in terms of quality of service. The system worked very poorly, with recurring image freezing events and consequent system non-responsiveness, as detected by the user. Even though the blind user was diverting from his path, the system gave no feedback to correct the trajectory, or gave feedback with a very long (unacceptable) delay.
•	in P3 the system worked with rather good quality. All the experiments during this time interval were successful, the user was able to receive all the expected feedback and complete his/her path. In fact, when his/her diversions from the established path resulted in deviations from the planned trajectory, the feedback worked properly, although with short but still acceptable delay.

The distinction of these working regions comes from problems which were external to the Tourist Eyes experiment and mainly related to traffic and users accessing the network, considering that the Millennium Square area in Bristol was very crowded at certain day time and many users at the time of the experiment execution were using their own applications and accessing the Internet. Also the weather conditions were very variable and unstable, and thus our experiment suffered of the bad propagation conditions.
 
 We analysed, for each of the three scenarios, the following parameters:
•	VM status, in terms of CPU and memory usage
•	Overall bitrate of IPCAM video flows entering the VM
•	Ping Delay
•	Ping message loss percentage.

