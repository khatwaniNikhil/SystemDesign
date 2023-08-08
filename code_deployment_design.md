# Functional Requirements
1. build,test, deploy - what all to be covered ?
2. build details
3. download code via SHA commits, 
4. how to build - out of scope, 15 mins build time, upto 10GB artifact
5. deploy details -  hunderds times a day, multiple regions, thousands of machines ?
6. testing - out of scope
7. trigger - code review, code merge, code ship? - code to be shipped

# Non functional Requirements
1. ETA to complete -  within 30 mins
2. availability - decent(2/3 nines) to support bug fix or release revert

# Architecture
Broadly 2 modules - Build and deploy

## BUILD module
1. job queue with each job event has SHA commit ID
2. Scale - 10k jobs to be run per day and single worker taking 15min to complete a job/100jobs per day, so need 100 workers
3. workers working over job queue(can be elastic set of workers if very dynamic load of jobs to be executed in parallel)
4. job tracking in db(table with status column status(queues, running, sucess, failed) and maintain heartbeat )
5. Separte polling layer to check/monitor last received heatbear for job and mark queued if threshold crossed
6. push binaries to S3 --- "global storage to async replication to regional storage”
7. replication status is tracked in db and marked deployable

## Deployment module
1. release readiness - global release bucket to regional release bucket
2. release deployment to actual servers - fast distribution of binaries challenge to thousands of servers - P2P 
3. Goal-state propogation via configuration service 
4. Global release version store(zookeeper/etcd) - source of truth service around which release version to be
currently deployed on all boxes, updated on user triggering a new release version
5. regional release stores continousally polling global store are also informed of changes
6. Machines are polling regional store trigger for changes and fetches build from P2P network 
