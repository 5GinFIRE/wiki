<!-- TITLE: Autoscaler -->
<!-- SUBTITLE: A quick summary of Autoscaler -->

# Autoscaler

1	Description of functionalities

1.1 How OSM’s stock autoscaler operates

The following figure depicts the high-level architecture of OSM’s (R5) stock autoscaler.

![Figure 1](/uploads/autoscaler/autoscaler-picture-1.png "autoscaler-picture-1.png")
 
Figure 1 High level architecture of OSM stock (default) autoscaler
For a VNF that includes a scaling descriptor, each VDU is monitored individually for the selected metric. Based on the retrieved metrics, the stock autoscaler can trigger a scale in or a scale out operation. For example, if at some point in time a VNF employs 3 VDUs with respective CPU loads 62% (VDU1), 93% (VDU2), 80% (VDU3) and the scale-out threshold is set to 60%, then the MON module (see above diagram) will place three scale-out alarm notifications in the Kafka bus. However, if the cooldown parameter is in use within the OSM configuration, only one of these alarms will result to an actual scale-out operation. For the rest of the alarms, the POL module will report that not enough time has passed since the last scale-out operation. Hence, it will skip the scaling request.

In the following, we will answer fair questions that have arosen by the 5GINFIRE consortium in relation with the cooldown parameter and how it affects our implementation and/or its comparison with the OSM default autoscaler which must operate with the cooldown mechanism.

[Q1] Is the cooldown parameter of the default autoscaler something that is thought to be activated/deactivated at will? Or is it something though to be permanently activated?

	The cooldown parameter is set in the VNF Descriptor level, inside a scaling-group descriptor and is fixed for the whole lifecycle of the VNF, so it cannot be changed for a running VNF.

[Q2] If the former is true, then your comparison is maybe unfair: you are comparing default autoscaler plus cooldown with new autoscaler without cooldown.

	For the implementation of our autoscaler, it would be required that the OSM code would have to be modified to allow for someone to define the number of VDUs by which to scale in the request towards the REST API of the NBI module. However, this functionality is not supported by OSM R5.
Thus, if the above functionality was available, we would also work using the cooldown timer and at the same time scale our VNFs as demanded by our load forecasting algorithms. 
Therefore, we bypassed the cooldown restriction in order to be able to issue multiple  scaling requests, each one scaling (in or out) e.g. one (1) VDU as per the configuration.
In any case, even the functionality to set the scaling step was available by OSM, our autoscaler would perform better than the default OSM autoscaler. The reason is that we use predictive analytics within our implementation, thus we can better ‘estimate’ the scale-in or scale-out curves and determine the best value for the scaling step. As an example, lets assume that many new requests arrive to OSM. The default OSM autoscaler would scale-out by a fixed step defined in its configuration (e.g. 2 VDUs). Instead, our predictive autoscaler may dictate that the best option to cope with the sudden load increase would be to scale-out with a scaling step of 10, i.e. we would allocate 10 more VDUs instead of only 2 (in the case of the default OSM autoscaler). This would allow clients to be served better by the system. Similar examples can be thought for the cases of scale-in scenarios. Our predictive approach allows a better estimation of the ‘scale-in’ curve and therefore, we obtain better performance in terms of decomissioning unessasary VDUs fairly more quickly than the default autoscaler of OSM. Concluding, we just ‘bypassed’ cooldown in order to run our implementation without requiring changes to the OSM codebase. 

[Q3] What would be the result if you used default autoscaler without cooldown?

	It is not possible to omit the cooldown field from the scaling-group descriptor. We tried that option and we observed tha the POL container crashes, complaining about missing cooldown parameter. However, it is allowed to use a cooldown value of 0, but this solution is not practical for the following reason: Setting the cooldown value to 0 would allow OSM’s default autoscaler to respond quickly to changes. However, remember that OSM monitors each VDU individually; in case of a zero cooldown timer, this would mean that at a certain evaluation interval, OSM could issue a scale-out for a VDU that exceeds the scale-out threshold and a scale-in for a VDU that exceeds the scale-in threshold, both at the same time. These actions would essentially cancel each other and may lead to system errors. The conclusion is that due to the above described behavior of the default OSM autoscaler with a zero cooldown value, it is not feasible to set the cooldown parameter to zero on the default OSM autoscaler.

Further to the above, there is a bug in OSM 5 release due to which, for a VDU that exceeds a scaling threshold there are actually two (2) alarm notifications sent from MON container instead of one. Thus, with a cooldown value of 0 these would result to duplicate scaling requests (since with cooldown equal to 0 all requests go through to LCM unfiltered). This would make the situation even more complecated. We reported this bug in OSM slack channel.


[Q4] I assume that without cooldown in the default autoscaler multiple changes would happen continuously and that everything would end up exploding somehow. If so, just include this type of ideas so that it does not seem unfair in the comparison,   and people understand that default approach would just not work without cooldown.

	This statemenet is very accurate. It would not be possible that the default approach works without a defined cooldown parameter.


1.2 How OSM_Autoscaler  operates

As explained in the previous section, OSM’s stock autoscaler respects the cooldown timer and thus it has to wait till the next evaluation interval in order to perform any additional scaling actions. However, should we configure the system with the manual scaling option (using the REST API of the NBI module) we are not restricted by the cooldown timer and this way one an issue as many scaling requests as required. This provides the opportunity to employ predictive algorithms (e.g. Holt-Winters, RNN, ARIMA) in order to predict sharp changes in the system’s load and accordingly undertake better performant scaling decisions.  As an example, if the scale-out threshold is set to 60 (per VDU) and the OSM_Autoscaler i) sees that the current number of VDUs is three and ii) the predicted total load (by the forecasting algorithm implemented inside OSM_Autoscaler) is 250, the OSM_Autoscaler will decide that two additional VDUs (remember that we have 3 VDUs supporting max 180 total load, thus with a predicted total load of 250 we shall need 3+2 VDUs supporting total load 5x60=300 which serves the requirements of the prediction of 250) are needed to support the raising load more efficiently (so that all VDUs have a load below the defined threshold of 60). Therefore, the OSM_Autoscaler trigger two scale out operations to cope with the phenomenon of the load increase. Analogous advantages are offered by the OSM_Autoscaler upon load decrease phenomena, where by our solution ‘shuts down’ faster unnecessary VDUs, saving energy to the NFV cloud provider.

The following subsection provides the high level architecture of our OSM_Autoscaler. The technical details of its implementation are described in detail in Deliverable D2 “Implementation report and operation plan”.

1.2 Architecture of OSM_Autoscaler

1.2.1 Architecture overview

The following diagram presents the architecture of our predictive autoscaler. As we can observe, it adds predictive functionality to OSM through the implementation of three main containters: i) the Metrics Collector container, ii) the Predictor container; and, iii) the PostgreSQL container. For a detailed description on the functionality of each container, the reader may refer to Deliverable D2.

 
Figure 2 Architecture of OSM_Autoscaler
 
2	2	

2	User manual

To use OSM_Autoscaler, the following steps must be followed.

Prerequisites:

1.	Include in your VNFD a scaling-group descriptor with scaling-type field set to “manual”. Example of a scaling-group descriptor:
 
Figure 3 Scaling-group descriptor
The description of the scaling settings follow the OSM information model and they are explained below: 
•	max/min-instance-count: The maximum/minimum number of scaling operations allowed. For example, if our VNF has initially 1 VDU, we can scale-out at maximum 11 additional VDUs. We selected this limit according to our OpenStack resources.
•	cooldown-time: The time (after a scaling operation) when no additional operations are allowed.
•	scale-in/scale-out-threshold/relational-operation: These settings are self-explanatory. If CPU utilization drops below 10%, a scale-in operation will be triggered. Conversely, if CPU utilization exceeds 60% a scale-out operation will be triggered.

•	vnf-monitoring-param-ref: The name of the monitoring parameter, corresponding to cpu_util metric of OpenStack’s telemetry system.

•	scaling-type: Can be automatic or manual. We used manual to replace the OSM stock auto-scaler logic with our own prediction-based auto-scaling stack. When running experiments, we alternated this between the two values, i.e. we used ‘automatic’ for experiments with the stock autoscaler and we used ‘manual’ for experiments with our own autoscaler.

•	threshold-time: The minimum time that cpu_util metric must cross a threshold in order that a scaling operation is ordered. For example, cpu_util must be over 60% for at least 60 seconds for a scale-out to be triggered. Note that we used this setting to avoid unnecessary scale-outs during a VDU’s start up time.

•	(vdu) count: The number of VDUs to commission/decommission with each scale out/in operation.

2.	Install your host machine (Linux OS recommended) with the Docker engine. 

3.	Ensure that OSM is reachable from your host system.
2.1 Installation guide
1.	Clone OSM_Autoscaler repo:
$ git clone https://github.com/5GinFIRE/OSM_Autoscaler.git
2.	Build the Docker containers:

$ cd prediction/
prediction$ docker build -t 5ginfire_predictors -f Dockerfile .
prediction$ cd ../metrics_manager/
metrics_manager$ docker build -t 5ginfire_metric_collector -f Dockerfile .
metrics_manager$ cd ..
3.	Initialize Docker swarm:

$ docker swarm init
4.	Create an overlay network for Docker swarm:
$ docker network create -d overlay –scope swarm gateway
5.	Deploy the docker stack:

$ docker stack deploy -c docker-compose.yaml mystack
6.	The OSM_Autoscaler stack should now be up and running. You may now proceed to configuration as described below in section 2.2.
2.2 Configuration

The OSM_Autoscaler can be customized via environment variables. Most common use cases  are provided below.
➔	The OSM_Autoscaler stack communicates with main OSM system to query performance metrics, e.g. the CPU utilization and accordingly issue scaling requests.
To point OSM_Autoscaler stack towards your OSM installation, you need to update the value of OSM_SERVER_IP variable as it is shown below (assuming that the IP address of OSM is 35.228.24.23):

$ docker service update --env-add OSM_SERVER_IP=35.228.24.23  mystack_metric_collector
$ docker service update --env-add OSM_SERVER_IP=35.228.24.23  mystack_predictors
➔	Three different prediction models are available: ARIMA, HOLT WINTERS and LSTM (RNN). The default model is ARIMA. To switch to a different prediction model during runtiime, update the value of PREDICTOR_MODEL variable as follows:

$ docker service update --env-add PREDICTOR_MODEL=LSTM mystack_predictors
➔	By default, OSM_Autoscaler evaluates the VNF system load every 120 seconds. To change the evaluation interval, update the value of DEFALUT_GRANULARITY variable as follows:

$ docker service update --env-add DEFAULT_GRANULARITY=60 mystack_metric_collector

