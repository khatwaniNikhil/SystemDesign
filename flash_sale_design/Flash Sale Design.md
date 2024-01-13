# Flash Sale
limited qty available for sale during a short span of time

# References
1. https://www.youtube.com/watch?v=x4CQlmXU06s
2. https://www.youtube.com/watch?v=UvShsiHJOOY
3. https://hackernoon.com/developing-a-flash-sale-system-7481f6ede0a3
4. https://www.alibabacloud.com/blog/double-eleven-technology-series-flash-sales-optimization-on-postgresql_594096
5. https://www.youtube.com/watch?v=mka-KSHsCJo&t=469s
8. https://www.youtube.com/watch?v=-I4tIudkArY
9. https://www.infoq.com/presentations/shopify-architecture-flash-sale/

# Functional 
1. in case of too high load, **backpressure** **but keep user engaged**(without showing HTTP 429 Too many requests)

    ![](https://github.com/khatwaniNikhil/SystemDesign/blob/main/images/ux_backpressure.png)
    
2. **First come first serve basis/fair throttling** - some user waiting endlessly versus some user get lucky nd placed order as soon as they came
3. **avoid undersell**: user dropped during the checkout process - payment failure, network issue etc.
4. **avoid over sell** - race conditions around deduction of inventory with highly concurrent user requests.
5. **identifying malicious users & bots** - tendency to purchase and later sale at higher price in secondary market
6. **Avoid mutiple charges** - leading to customer complaints to credit cards and merchant has to bear dispute fees

# Non Functional
1. handle **high read** load - storefront
2. handle **high write** load and interact with external services - checkout 
    1. Inventory deduction throughput of a Single SKU upto 1k per sec - flipkart flash sale numbers

![](https://github.com/khatwaniNikhil/SystemDesign/blob/main/images/flash_sale_read_write_basis_business_modules.png)

4. high availability
    1. users are able to buy products, merchants able to sell products easily
5. low latency
6. scalable
7. Observability
8. manage ddos: unfair means will be used due to competitiveness and limited inventory on sale
9. considering high profile social media celebrity sell their white labelled inventory - social media customer back lash to be avoided at any cost

# Scale Assumptions
1. Around **100 orders per sec**/10 million orders in day
   
# System Design
##  Challenge1: High Checkout Load leading to crashes/unavailability 
1. Checkout with its massive number of writes
2. every checkout session created a new record in MySQL and, exacerbating that, every step in the flow modified that same record.
3. Long term solution: Refactor of the entire checkout flow to condense the number of writes into one, this was not feasible in limited time.

##  Solution: Throttling with user engagement 
1. leaky bucket algo. to throttle user, redirect the throttled user to queued page which is cached by LB.
2. throttled page javascript will keep polling the /checkout endpoint to see if there is enough capacity. For users with Javascript disabled, we use meta refresh.
3. When enough capacity available, LB gives go ahead to checkout, we place securely signed cookie in user session to avoid rethrolling the same user and let him complete checkout
4. Tech stack: Nginx + Lua scripts + LB + javascript polling/Meta fresh + signed cookie(to avoid rethrottle once allowed to checkout)
   
        ![](https://github.com/khatwaniNikhil/SystemDesign/blob/main/images/checkout_throttle_handling.png)

## Challenge2:  **UnFair throttling** leading to bad customer experience and social media/twitter backlash 
1. Users who were throttled and asked to wait(added to queue) should be served on first come first basis

## Solution
   1. not exactly stateless -
       1.1 as each LB will have state internally but no state across LB.
       1.2 Additionally, each user is provided with secure signed cookie contianing its first request time.
   2. Feedback systems based optimisation and lag delimeter
       1. Picture a thermostat optmising for customer set temp. - let say we want to set room temp. to X and thermostat sense current room temp. and set commands to airconditioner acccordingly inc./dec. room temp. but at the same time outside chill or sunlight will also interfer and sensor will sense and pass that as feedback.
       2. Similarly lag delimeter(time elapsed since start of sale) will be continously tuned based on current request load to achieve goal of no of requests/capacity of a leaky bucket of a LB(shopify used 16 size leaky bucket).
        1. requests are categorised based on lag delimeter (per LB) into high priority and low priority requests.
        2. Low priority immediately throttled, high priority go through leaky bucket. During non sale hours - lag delimeter is current time(all requests are high priority and no one is throttled).
        3. low priority requests are immediately throttled and high priority buckets were passed to leaky bucket.
        4. capacity of each leak bucket = total capacity/no. of load balancers.
   
6. prevents ddos
   1. blacklisting & whitleisting ip support
   2. per ip limit no. of reqs in a min. etc.
   3. limit max connections to webserver
   4. close slow connections
7. event loop based architecture - scalable to high load of http requests
8. customised nginx - openresty as application load balancer
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
4. idempotent key based api design to avoid duplicate charges
 
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

