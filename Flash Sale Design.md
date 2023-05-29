# Flash Sale
At the most basic level, they r limited availability sales in qty or time

# References
1. https://www.youtube.com/watch?v=x4CQlmXU06s
2. https://www.youtube.com/watch?v=UvShsiHJOOY
3. https://hackernoon.com/developing-a-flash-sale-system-7481f6ede0a3
4. https://www.alibabacloud.com/blog/double-eleven-technology-series-flash-sales-optimization-on-postgresql_594096
5. https://www.youtube.com/watch?v=x4CQlmXU06s
6. https://www.youtube.com/watch?v=x4CQlmXU06s
7. https://www.youtube.com/watch?v=mka-KSHsCJo&t=469s
8. https://www.youtube.com/watch?v=-I4tIudkArY

# Functional 
1. in case of too high load, backpressure with proper message to buyer

    ![](https://github.com/khatwaniNikhil/SystemDesign/blob/main/images/ux_backpressure.png)
    
2. avoid undersell: user dropped during the checkout process - payment failure, network issue etc.
3. avoid over sell - race conditions around deduction of inventory with highly concurrent user requests.
4. identifying malicious users

# Non Functional
1. handle high write load - checkout driven
    1. Inventory deduction throughput of a Single Product upto 1k per sec - flipkart flash sale numbers
3. high availability
    1. users are able to buy products, merchants able to sell products easily
5. low latency
6. scalable
7. Observability
8. manage ddos: unfair means will be used due to competitiveness and limited inventory on sale
9. considering high profile social media celebrity sell their white labelled inventory - social media customer back lash to be avoided at any cost

# Scale Assumptions
1. Around 110 orders per sec - 10 million orders in day
2. Based on flipkart past sale no's, 600 million requests during flash sale, on avg. 6 million req per day on non flash sale service

# System Design
## Nginx 
1. write flows related optimisation - query tuning etc.
2. API throttle using leaky bucket algo
3. prevents ddos
   1. blacklisting & whitleisting ip support
   2. per ip limit no. of reqs in a min. etc.
   3. limit max connections to webserver
   4. close slow connections
4. event loop based architecture - scalable to high load of http requests
5. customised nginx - openresty as application load balancer
    1. shopify used openresty - written on top of nginx - adding luaJIT to write lua scripts to modify the flow of requests 
        1. shopify leveraged the same to create http router to enable multi dc
        2. muti dc caching framework
        3. additional logging


## Cache 
1. Cache api responses which r mostly static like item description

## Server
1. until user clicks checkout, manager cart via redis.
2. Increasing availability and response time of external facing application server
    1. offloading work to background workers via mq
        1. on new user signup - sending email can be done asyncronously
        2. image processing on upload to website - 
        3 server response is somewhat the image has been uploaded successfully, it will appear on the website later if things go as planned
4. scaling of stateless api server
 
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

