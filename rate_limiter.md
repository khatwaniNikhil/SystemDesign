# References
https://github.com/Salah856/System-Design/blob/main/Design%20Rate%20Limiter.md

# Why rate limit
1. prevent ddos
2. resource fairness across different api clients
3. manage cost & resources efficiently

# Granularity
api or network level
client level or api level or client-api level

# Client side/server side rate limit
unless trusted client and common SDK,  client side not in full control - security & ops(dynamic change)
server side preferred

# Throllting basis
IP, userId, api-key

# NFR around rate limiter
low latency 
highly Available
support distributed set of servers leveraging the rate limiter
over period of time when new clients are added - maintain SLA for new & existing clients

# ways to handle rate limited requests
decline or push over queue and execute later

# user notification
429 status code with header X-RateLimit-RetryAfter

# HLD
1. leverage API gateway or other solution below level/server level
2. choose best suited algo for rate limit: token bucket, leaky bucket, fixed window, sliding window
3. use redis
   1. counters around client api hits in current time interval
   2. rate limit rules/thresholds
3. rate limit rules are persisted in a db

![](https://github.com/khatwaniNikhil/SystemDesign/blob/main/images/ratelimit_hld.png)
   




