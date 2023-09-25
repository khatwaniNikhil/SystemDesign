# References
http://highscalability.com/blog/2012/5/16/big-list-of-20-common-bottlenecks.html
https://medium.com/@pavan.baburao/how-to-scale-backend-239221fe7bb0

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
   1. ordering events
5. Memory
   1. OOM
   2. Swap overhead
   3. Memory fragmentation 
7. Network
   1. NIC maxed out

# Prioritse effort as per latency hierarchy: 
N/w > disk IO > Memory > CPU

## N/W: waiting for n/w requests to complete
1. internal api overloaded - horizontally scale by adding more machine behind LB
2. external api - batch requests + cache response(if not changing very frequently) to avoid duplicate hits
3. consider GRPC over json

## DISK
common db side tuning areas:
### READ fix
1. buffer pool cache optimise
2. enable query cache
3. better indexes
4. Read replicas

### WRITES
1. batch commits to db
2. async writes to db
3. logging write thresholds - rate limit and alerts around it

## Memory: 
profiling code paths +  gc logs debugging +  heap dump analysis

## CPU: 
CPU profilers(flamegraphs)

## TOOLS
strace, top/htop, iotop, iftop, netstat, gdb, flamegraph, opentracing tools (zipkin/jaeger/AWS X-Ray)
	


