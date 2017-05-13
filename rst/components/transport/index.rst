###########################
Transport
###########################

	This part exaplains how transport of data between various components of Holmes is being made. 

Transport's main task is 

	 1. To move data among the Planners.
	 2. To monitors the health of the Planners and performs remediation actions to support QoS. This enables a robust level of resilience by ensuring that requested work is always stored in a queue and that results and taskings are never lost

 The Transport consists of four main sections (T1, T2, T3, and T4) as Figure 2 illustrates. We have optimized technology to handle the interaction among Planners. In the following we introduce these sections.

#### Transport T1: 

 T1 is focused on moving data to the Investigation-Planner(TOTEM) for analysis. To do this, T1 is required to perform three primary actions. The
 
  1. To receive tasking from the Gateway and schedule their transmission to the TOTEM.
  2. To receive tasking from T2 and submit them to the Gateway for validation. 
  3. To monitor the health of the Interrogation Planner and perform QoS management. ( TODO )

When implementing T1, we recommend the utilization of a distributed message broker such as Kafka or RabbitMQ.

#### Transport T2:

 T2 is focused on receiving objects and results from the Investigation(TOTEM) and Interrogation Planners. To do this, T2 has two primary actions. 
 
 1. To receive data from the Investigation and Interrogation Planners and schedule their submission to the T3 for Storage. This is separated from T3 to allow a message queue service to be implemented to help throttle the storage of data during peak loads as storage operations can be costly but are often not time critical.
 2. To receive objects from the Investigation and Interrogation Planners and pass them to T1 for further analysis. 

Like T1, we recommend the use of a distributed message broker.


#### Transport T3:

 T3 is focused on submitting and retrieving data from the Storage Planner. As such, T3 is responsible for three primary actions: 

1. provide data  from the Storage Planner directly to the Investigation and Interrogation Planner
2. receive information from T2 and pass the data on to the Storage Planner
3. manage the QoS of the Storage Planner. ( TODO )

We did not use any message queues between the Storage Planner and the databases. This is because databases are often heavily optimized for retrieval of data and implement their own form of message queues.

#### Transport T4:

 T4 handles the exchange of information between the Interrogation and Presentation Planners. As such, T4 is responsible for 2 primary actions. 

 1. To provide a conduit for communication between the Presentation and Interrogation Planners.
 2. To monitor the health of the Investigation Planner and perform QoS management. As the Interrogation Planner will typically provide data through HTTP calls, we recommend implementing T4 as HTTP load balancers. ( TODO ).

