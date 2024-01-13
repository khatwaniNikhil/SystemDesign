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

##  Solution: Throttling with user engagement with random polling
1. query optimisation is a good start but eventually too many requests will need backpressure support
2. LB level capacity checks via leaky bucket algo. to throttle user, redirect the throttled user to queued page which is cached by LB.
3. throttled page javascript will keep polling the /checkout endpoint to see if there is enough capacity. For users with Javascript disabled, we use meta refresh.
4. When enough capacity available, LB gives go ahead to checkout, we place securely signed cookie in user session to avoid rethrolling the same user and let him complete checkout
5. Tech stack: Nginx + Lua scripts + LB + javascript polling/Meta fresh + signed cookie(to avoid rethrottle once allowed to checkout)
   
        ![](https://github.com/khatwaniNikhil/SystemDesign/blob/main/images/checkout_throttle_handling.png)

## Challenge2:  **UnFair throttling** leading to bad customer experience and social media/twitter backlash 
1. Users who were throttled and asked to wait(added to queue) should be served on first come first basis.
2. Storing timestamp of first attempt of throttled users in a data store and each user polling request compare the same with other pending requests in data store. **Data store as **new point of failure** with cross dc replication is non trivial and will add latency**.

## Solution: Stateless* Fair queueing
1. Instead of maintaining data store: store threshold as state on each load balancer, each user is provided with secure signed cookie contianing its first request time and have two step process
   1. First step to decide users whose first request timestamp closess to threshold and allow them to proceed to second step.
   2. In second step, throttle first step pass set of users using leaky bucket based check and polling for retries.  

## Challenge3: Skewness created by users who made early first requests but closed without waiting at polling page before they get chance to checkout
## Solution: Self adjusting threshold based on incoming traffic
1. Inspired from control systems and PID controller/ digital climate control in your home - Idea is to keep calculating how far we are from goal and then calculate correction required to reach goal. Picture a thermostat optmising for customer set temp. - let say we want to set room temp. to X and thermostat sense current room temp. and set commands to airconditioner acccordingly inc./dec. room temp. but at the same time outside chill or sunlight will also interfer and sensor will sense and pass that as feedback.
2. Threshold/lag delimeter is compared with first request by user timestamp in cookie to categorise incoming requests into low and high priority. Low priority were instantly throttled and high priortity were passed to leaky bucket. Capacity of leaky bucket(no of high priority requests to allow) was local(total capacity/no. of LBs) to load balancer to avoid global shared state across Load balancer's in the cluster
3. How it works: let say lag delimeter starts with current time and all requests as they arrive after current time are categorised as high priority and eventually leaky bucket of a LB gets full. The threshold is dynamic, so that once the bucket fills up, the threshold demarcates requests into high and low priority.  Once room opens up again, the threshold recalculates, and the appropriate number of low priority requests become high priority. LB adjusts lag delimeter so that no. of requests hitting the bucket are not more than capacity of that LB. If at any instant more than current lag delimeter requests hit all are throttled.

## Challenge4: Handling DDOS
## Solution
1. blacklisting & whitleisting ip support
2. per ip limit no. of reqs in a minute
3. limit max connections to webserver
4. close slow connections

## Challenge5: Handling DB side consistency and performance challenges
## Solution: Advisory locks instead of row level locks or select for update no wait
1. row level lock for inventory update: lot of contention in case of million req/sec
2. select for update no wait - customer waiting time can be reduced using sql no wait where customer req thread does not wait for db level lock but user exp. still be bad as we will have to tell him to try again later
3. first level advisory lock with second level row lock 
    1. user enforced db level lock does not need to lock row/table in db. its object is an integer - lightweight & highly performant as does not need to db row or table level data
    2. use db entity PK/ID field for adivosry lock key
    3. QPS of single advisory lock in handling concurrent update requests for one record can reach 391,000/s
    4. 32-core, 64-thread computer can handle 1.31 million requests per second.
    5. the state of product will be changed to sold out quickly, avoiding a waste of database resources.
    6. advisory lock reduces cpu utilisation drastically

## Other Architecture Choices
1. Cache api responses which r mostly static like item description
2. until user clicks checkout, manager cart via redis.
3. Increasing availability and response time of external facing application server
    1. offloading work to background workers via mq
    2. on new user signup - sending email can be done asyncronously
    3. image processing on upload to website - server response is somewhat the image has been uploaded successfully, it will appear on the website later if things go as planned
4. idempotent key based api design to avoid duplicate charges
5. overprovision infra dedicated to sales
6. store data in binary format like avro or protobuf for memory gains
 
## UI side tradeoffs
1. load balancer with auto scale suppoort of UI artifacts
2. CDN hosting of static assets
3. avoided repeated click by user: disable submit btn once clicked
4. avoid unnecessary multiple api calls
    1. Lodash-debounce: for example - time threshold based wait for user typing to complete before next api hit to backend
    2. Axios HTTP Request Cancellation: for example - video streaming user has jumped to future time, just immediate chunk is no longer to be fetched

