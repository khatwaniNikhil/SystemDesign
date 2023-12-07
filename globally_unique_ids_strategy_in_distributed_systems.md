# Approach 1  - Using AutoIncrement Id via multi master db nodes
1. With K db nodes, each generate auto increment id's with offset of K across two id's generated on same node. 
## references
1. https://www.pythian.com/blog/case-auto-increment-mysql
2. https://www.cockroachlabs.com/blog/the-hot-content-problem-metadata-storage-for-media-streaming/
3. https://www.slideshare.net/davegardnerisme/unique-id-generation-in-distributed-systems

## challenges
1. it has scaling issues as nodes are added, removed.
2. can lead to hotspots - values generated around the same time have will often be similar and thus may be located close to each other in the table’s storage. 
3. using auto increment id as PK of table will lead to any business need based access pattern needs alteast two hops - "secondary index which looks up autoincrement based PK index for actual data fetch". Since mysql table data is ordered by default by PK(clustered index), any non auto increment id key will be more efficient.
4. auto increment leads to table level locks while txn commits and can lead to deadlocks also.
5. when using auto increment with a mysql cluster(async replicatio to slaves), in case of master failure - it is risky to apply missing operations from the original master binlog to the new master.

# Approach 2 
https://code.flickr.net/2010/02/08/ticket-servers-distributed-unique-primary-keys-on-the-cheap/
1. id generation separated from data store
2. centralised auto increments without single point of failure
3. one node generating 1,3,5,7 id's and other generating 2,4,6,8 etc.
4. odd id's and even id's could drift away in case of downtimes of one of the id generating nodes.

 # Approach 3 - UUID
https://www.cockroachlabs.com/blog/what-is-a-uuid/
https://dev.mysql.com/blog-archive/mysql-8-0-uuid-support/

1. the keys are unique across tables, databases and servers - uses data time, random value and mac address of machine. 
2. are hard(er) to guess (example from an URL)
3. can be generated offline (without any exchange of information with the database or collaboration with other components of the system)
4. simplifies replication

## challenges(mysql 8 has made enhancements to handle the same) 
1. storage space is high - 36 alpha numeric(128 bit). If used as PK and considering in mysql secondary indexes contain PK id,
secondary index becomes difficult to keep in memory. To save space UUIDs should be stored as binary(16) values as mentioned https://www.pythian.com/blog/case-auto-increment-mysql
2. Not being ordered

# Approach 4 - Custom key generators - Twitter snowflake
https://abheist.com/twitter-snowflake-for-unique-ids/
https://blog.twitter.com/engineering/en_us/a/2010/announcing-snowflake
1. based on timestamp, datacenter id, machine id, sequence no((generated on a single machine within a millisec internal - post that it is reset to zero)
2. seq no allows it to generate 4096 unique id's per millisec .
3. timestamp 41 bits make it sortable by time.
