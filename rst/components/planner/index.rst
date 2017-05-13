###########################
Planner
###########################
	
 The Planner's primary purpose is to serve as an intelligent orchestrator for Services. At its core, it manages tasking, allocates resources, enforces the ACL and provides an abstraction between the Services and other aspects of Skald. Transport what Services are available for tasking and provides status information back to the system core. The Planners are loosely coupled with other parts of Skald. In this way, they provide flexibility by ensuring that changes to the core aspects of a Planner will not affect other parts of the system.

Planner Themes are 
	 
*************	 
1. Gateway
*************

	The Gateway's primary purpose is to receive taskings and push it to the Transport. When tasking is received, the Gateway first performs an ACL check to guarantee that the tasking is authorized.  If authorized, Gateway then ensures taskings are valid, scheme compliant, and that the pipeline can handle the requested work. The Gateway can also automatically assign tasking based on an object type. Together this ensures that pipeline resources are not wasted and provides the first level of system security.


*****************
 2. Investigation
*****************

This theme is responsible for performing feature extraction against objects. When tasking is received, it schedules the execution of its Services which are capable of performing **static** and **dynamic** analysis as well as gather data from third parties. As such a failed service will not hinder other services and the planner itself.

This was implemented in HolmesProcessing as 2 components. Holmes-Totem, Holmes-Totem-Dynamic. 

TOTEM:
==========

		Implemented in Scala, TOTEM was developed concurrently to the definition of the Skald framework.

		
	
TOTEM terms:
^^^^^^^^^^^^^
 TOTEM’s architecture is optimized towards preventing unneeded network transfer of files. TOTEM is somewhat different from other stream processing systems, so it is important to clearly state the language that will be using to describe the system going forward.

Worker :
----------
	A specific instance of a TOTEM worker system, separate from the Message Queue. Each Worker is a self-contained system of processes, which coordinate with each other on the local system, and are capable of executing a predefined set of analytic tasks. Workers have no knowledge of other members of their swarm, and only communicate with the transport layer

Job:
--------
 	A structure which details the location of a file to analyze, the individual analysis tasks to be performed, any associated information or context needed to perform those tasks, and the number of times this overall job has been attempted.
 
 Work:
 --------
 	An individual analytic process to be executed. Given the example of having a Job where the file in question is a PDF document, a possible task would be the extraction of textual data from that document.

 Work Results:
 -------------------
 	The results returned from a single analytic process. A Job which describes three Work units - the hashing of a file, application of Yara rulesets against that file, and the submission of that file to VirusTotal - would generate three Work Results. These results can analytic successes, failures, or errors. 

 Eviction:
-----------
 	The removal of a Job from a worker’s local store. This occurs when the job has resolved as a success, or failure, with regards to operations performed, and not necessarily the analytic result. TOTEM has a concept of partial completion - Work which needs to be requeued are re-queued, whereas Work which is completed is forwarded on, even within Jobs that result with a combination of these results.

TOTEM's Concurrency:
^^^^^^^^^^^^^^^^^^^^^

	TOTEM’s concurrency model is based on Actors, implemented through the Akka project. It is important to note that Actors are only to be used for stateful concurrency, hence our actual calls to analytic services are done via Futures. Our services are connected over HTTP/S REST interfaces, and are language agnostic. Unlike other distributed systems, each TOTEM instance is effectively stand alone. TOTEM’s communication is message-centric, and form the sole means of sharing information between multiple worker nodes.

	Messages both from exterior source, and interior sources, contain all the context appropriate for that message. This means that a new work element details to each worker what work needs to be done, where the resource of operation is located, and what, if any, optional commands should be performed. These messages are persistent throughout the message queue without explicit acknowledgment of completion or failure, no messages are removed from the message queue.

*************
 3. Storage
*************

 	This Planner controls how data is stored and retrieved in Skald. At its core, it is an abstraction layer for the database Services. It enforces a standard storage scheme and passes the requests to the appropriate database elements  This enables Skald  to be storage system agnostic and utilize a single or hybrid data storage scheme for resultant data and objects of analysis. The benefit of this approach is that data can be stored in databases optimized for the data type while also easing the inclusion of legacy archives. For example, objects can use Amazon's Simple Storage Service (S3) while the features can be stored in a system optimized for textural data such Cassandra.

 This is implemented in HolmesProcessing as Holmes-Storage.

*******************
4. Interrogation
*******************

 	The Interrogation Planner focuses on how to turn the extracted features into intelligence in two distinct forms.

********************
5. Presentation
********************

	This Planner provides a standard mechanism for interacting with stored data and the data the Interrogation layer generates
