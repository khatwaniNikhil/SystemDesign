# References
https://queue.acm.org/detail.cfm?id=2745840

# Scalable Distributed Cron Service
Another way to look at it is large scale scheduled Payments settlement engine 

## Principles - Unix Cron
1. crontab as the time schedule specification
2. crond daemon loads list of jobs from crontab, keep them sorted, waits until next execution, fires the execution and then repeat the loop.

## NFR
1. Strong Consistency & Idempotency(Skip launch versus double launches)
   1. Idemptotent example - garbage collection scheduled task
   2. Non Idempotent example - sending a bulk marketing email to clients, payment transaction
Considering undoing the action of double launch is complicated/not possible we will prefer skip launch over double launch. 

2. Reliability
    1. Unix single machine crontab and crond will becomd spof. crond works in fire and forget mode and does not store any data around execution stats.
    2. anacron utility has recovery/retry support in case of failures. It maintains a file with timestamp of last run for each cron. 
    3. Two node setup where one is scheduler and other is executor of jobs. Scheduler launches over executor node via ssh. Other than Scheduler, executor could fail, connectivity b/w the two is also a point of failure.
    4. Partial Failure witin datacenter should have minimal effect on cron schedule runs for example, a single power distribution unit taking out all the Cron processes. Replicas across different locations within data center is the key here.
       
3. Scaling

## Approach
1. Use Paxos/Zab/Raft algorithm to ensure consistent state across replicas - idea is to achieve a consensus on state changes among distributed unreliable nodes/replicas.
2. Replicas across different locations within data center to mitigate parital datacenter failures.
3. In case of tight schedules like run every 1min where reschdeuling will bring lot of delays, we should keep some standby hot nodes which can be quickly leveraged in case of failures.

## System Design - Components breakup
![](https://dl.acm.org/cms/attachment/8b2067dc-0d61-40b3-9cc8-c731d1b17d24/petoff1.png)
![](https://dl.acm.org/cms/attachment/af2ce86c-c3e7-454d-a1c2-514ac773c198/petoff2.png)

##### Paxos Master | Cron Master: 
1. Master maintains crontab schedule in sorted order and once scheduled time is reached, it makes announcement to the paxos group, waits for ack and then only make RPC calls to data center scheduler for allocating nodes to execute cron. Cron master will also have to handle any cron job execution failures.
2. As soon as mastership is lost, master should not communicate with datacenter scheduler to avoid any conflicting actions and bring inconsistency around job execution/double launches.

##### Datacenter scheduler
Separate task from machine - Relaible data center scheduler system will dynamically assign worker nodes for execution of tasks once schedule is reached.
   
##### Paxos replicas | Cron Executors:  
keep track of the state of the world, as provided by the master, in order to take over at a moment's notice if needed


##### Storing State 
Paxos is essentially a continuous log of state changes, appended to synchronously with the state changes. This has two implications:
1. the log needs to be compacted, to prevent it from growing infinitely -  snapshots at regular intervals
2. the log itself must be stored somewhere
    1. logs and snapshots stored on the local disk - Storing logs on a distributed file system can come with a substantial performance penalty caused by frequent small writes. Losing small amount of logs since the last snapshot in case of all replica node failures and idempotency glitches is the tradeoff here.
    2. snapshot backups are more suited for distributed file system storage.
3. freshly started replica can fetch the state snapshot and all logs from an already running replica over the network. This makes replica startup independent of any state on the local machine, and makes rescheduling of a replica to a different machine upon restart (or machine death) essentially a non-issue for the reliability of the service.
