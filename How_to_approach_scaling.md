# References
1. http://highscalability.com/blog/2012/5/16/big-list-of-20-common-bottlenecks.html
2. https://medium.com/@pavan.baburao/how-to-scale-backend-239221fe7bb0
3. https://www.linkedin.com/pulse/boosting-your-applications-scalability-why-grpc-future-albin-joseph/

# Step 1 Identify component which needs scaling
1. Network
2. Database
3. Disk
4. Memory
5. CPU
6. Caching
7. OS

## Common bottleneck examples 
1. DB
   1. Working size exceeds available RAM
   2. Large joins taking up memory
   3. lock contentions
   4. read/write access patterns
2. Disk
   1. Random IO
   2. Disk fragmentation   
3. CPU
   1. Context switches
   2. CPU overload - GC Pauses
4. Queue
   1. produce consume rate mismatch leading to queue depth on higher side
5. Memory
   1. OOM
   2. Swap overhead
   3. Memory fragmentation 
7. Network
   1. NIC maxed out

# Prioritse effort as per latency hierarchy: 
N/w > disk IO > Memory > CPU

## Network
### Symptoms
waiting for requests to complete

### Tools for network monitoring
1. https://www.baeldung.com/linux/monitor-network-usage
stats per network interface, per connection socket, per respective process id
nload, speedometer, iftop, nethogs  

### Tuning tips
1. Reduce requests over network - use batching, caching response(if not changing very frequently) to avoid duplicate hits
3. consider GRPC over json:
   1. binary protocol over text(json) is much more efficient/reduced network overhead
   2. http/2 request multiplexing, bidirection streaming support, server push and header compressin
   3. advanced features like flow control and request cancellation

## DISK
common db side tuning areas:
### READ fix
1. tune mysql buffer pool(recently used data's in-memory cache).
2. To start with avoid Query Cache as it is disabled by default from mysql 5.6.
3. revisit db indexes
5. Read replicas

### WRITES
1. batching commits to db
2. queuing requests with async writes to db
3. Throttling based on thresholds

## Memory: 
profiling code paths +  gc logs debugging +  heap dump analysis

## CPU: 
CPU profilers(flamegraphs)

## TOOLS
strace, top/htop, iotop, iftop, netstat, gdb, flamegraph, opentracing tools (zipkin/jaeger/AWS X-Ray)
	


