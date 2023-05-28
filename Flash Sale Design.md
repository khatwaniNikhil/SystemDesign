# References
1. https://www.youtube.com/watch?v=x4CQlmXU06s
2. https://www.youtube.com/watch?v=UvShsiHJOOY
3. https://hackernoon.com/developing-a-flash-sale-system-7481f6ede0a3
4. https://www.alibabacloud.com/blog/double-eleven-technology-series-flash-sales-optimization-on-postgresql_594096
5. https://www.youtube.com/watch?v=x4CQlmXU06s
6. https://www.youtube.com/watch?v=x4CQlmXU06s

# Functional 
1. avoid undersell: payment not completed by user, giving chance to other users
2. avoid over sell 
3. identifying malicious users

# Non Functional
1. handle high concurrency/load
    1. Inv. Deduction Throughput of a Single Product upto 1k per sec
3. high availability: user does not see failure
4. low latency
5. scalable
6. Observability
7. manage ddos: unfair means will be used due to competitiveness and limited inventory on sale

# Scale Assumptions
1. Around 110 orders per sec - 10 million orders in day
2. Based on flipkart past sale no's, 600 million requests during flash sale, on avg. 6 million req per day on non flash sale service

# System Design
## Nginx 
1. prevents ddos
   1. blacklisting & whitleisting ip support
   2. per ip limit no. of reqs in a min. etc.
   3. limit max connections to webserver
   4. close slow connections

## Cache 
1. Cache api responses which r mostly static like item description

## Server
1. Async request processing at server level via request queing to MQ etc.
2. scaling of stateless api server
 
## Database
1. row level lock for inventory update: lot of contention in case of million req/sec
2. select for update no wait
    1. customer waiting time can be reduced using sql no wait where customer req thread does not wait for db level lock but user exp. still be bad as we will have to tell him to try again later
2. first level advisory lock with second level row lock 
    1. user enforced db level lock does not need to lock row/table in db. its object is an integer - lightweight & highly performant as does not need to db row or table level data
    2. use db entity PK/ID field for adivosry lock key
    3. QPS of single advisory lock in handling concurrent update requests for one record can reach 391,000/s
    4. 32-core, 64-thread computer can handle 1.31 million requests per second.
    5. the state of product will be changed to sold out quickly, avoiding a waste of database resources.
    6. advisory lock reduces cpu utilisation drastically

## Architecture
1. overprovision infra dedicated to sales
2. store data in binary format like avro or protobuf for memory gains
3. read replicas versus cache
     1. read replica will be size constrained as each read replica will store complete data
     2. cache will be faster and size can be tuned/managed via TTL 

## UI
1. load balancer with auto scale UI service
2. CDN hosting of static assets
3. avoided repeated click by user: disable submit btn once clicked
4. avoid unnecessary multiple api calls
    1. Lodash-debounce: for example - time threshold based wait for user typing to complete before next api hit to backend
    2. Axios HTTP Request Cancellation: for example - video streaming user has jumped to future time, just immediate chunk is no longer to be fetched

