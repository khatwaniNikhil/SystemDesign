# Assumption: don’t think about rate limiting and security
![](https://github.com/khatwaniNikhil/SystemDesign/blob/main/images/Bruteforce_Password_Retriever.jpeg)
# Server
1. (Syncronous API endpoint) - generate in memory next quota of range of potential passwords on request basis 
2. batch exec. and mgmt.
	2.1 Loop until password not found(resumable from last run on failures)
	2.2 persist state around current over db
	2.3 delegate work to pool of workers
	2.4 worker health check
	2.5 handle worker die - handover pending work to another available worker or queue up if no worker currently available
	2.6 worker notification handling
	2.7 found valid password & stop current ongoing workers
	2.8 finished current subbatch of work
	2.9	all workers work done but no valid passwd found, go for next batch
	2.10	persist already completed sub batches in db

# Workers 
hit twitter for current sub batch and notify server

# Workers running — FREE COMPUTE RESOURCES
Server  — Private cloud
