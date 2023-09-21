# Priority: 
N/w > disk IO > Memory > CPU

## N/W: waiting for n/w requests to complete
2.1 internal api issues: might need to add more machines behind LB 
2.2 external api	   : batch requests + cache if any duplicate calls
2.3 Use ENVOY PROXY    : GRPC support(instead of json over rest) + HTTP1.1 to HTTP 2  for better req. multiplexing	

## DISK
### READ fix
1. buffer pool cache optimise
2. enable query cache 
3. better indexes
4. SQL DB - consider sharding 
5. NoSQL - add seconday indexes

### WRITES
1. batch commits to db
2. async writes to db
3. logging write thresholds - rate limit and alerts around it
4. arch. revisit - db hot cache / cold storage model:
5. keep the hot cache on SSD disks and cold storage on HD’s or distributed storages like HIVE which uses HDFS as underlying storage.

## Memory: 
profiling code paths +  gc logs debugging +  heap dump analysis

## CPU: 
CPU profilers(flamegraphs)

## TOOLS
strace, top/htop, iotop, iftop, netstat, gdb, flamegraph, opentracing tools (zipkin/jaeger/AWS X-Ray)

## Concepts
Max File Descriptors for a machine
TCP/IP states for open connections, 
Shared Memory, 
HTTP1.1 vs HTTP2, 
WebSockets, 
OOM kill	


