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


