# scalable payment settlement engine(TODO)

20million scheduled payments a day - 1 or more time(recurrent)

Functional req
1. handling request submission ...write heavy
2. 


Non functional req
1. availability
2. data integrity & consistency  
	no loss of data/user request
	accurate balance before/after txn

Availability
Reliability
Security
Consistency
latency
throughput
ACID + relationships



capacity planning
1. req/sec: 200million/10pow6 sec = 2k req/sec
2. data size: 
	payload of single request: 1K = 20GB day/ 8TB yr
		userId, deductAmount, destinationDetails(bank details), transferType, cron_schedule


ELB/ALB  ->   API server statelesss cluster  -> AWS RDS		<- TaskServer(Quartz scheduler)
											(archival of more X yrs old data in s3 glacier)

									MQ		
