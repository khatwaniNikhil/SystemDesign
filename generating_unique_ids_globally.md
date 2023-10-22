# Approach 1  - AutoIncrement Id
## references
1. https://www.pythian.com/blog/case-auto-increment-mysql
2. https://www.cockroachlabs.com/blog/the-hot-content-problem-metadata-storage-for-media-streaming/

## challenges
1. auto increment is not feasible for global uniqueness as soon as db is shared across multiple nodes.
2. in distributed systems setup, using autoincrement in mysql/serial in postgres can lead to hotspots -
values generated around the same time have will often be similar and thus may be located close to each other in the table’s storage 
3. using auto increment id as PK of table will lead to any business need based access pattern needs alteast two hops -
secondary index which looks up autoincrement based PK index for actual data fetch. Since mysql table data is ordered by default by PK(clustered index), any
non auto increment id key will be more efficient.
4. auto increment leads to table level locks while txn commits and can lead to deadlocks also.
5. when using auto increment with a mysql cluster(async replicatio to slaves), in case of master failure - it is risky to apply
missing operations from the original master binlog to the new master.

 # Approach 2 - UUID
https://www.cockroachlabs.com/blog/what-is-a-uuid/
https://dev.mysql.com/blog-archive/mysql-8-0-uuid-support/

1. the keys are unique across tables, databases and servers
2. are hard(er) to guess (example from an URL)
3. can be generated offline (without any exchange of information with the database or collaboration with other components of the system)
4. simplifies replication

## challenges(mysql 8 has made enhancements to handle the same) 
1. storage space is high - 36 alpha numeric(128 bit). If used as PK and considering in mysql secondary indexes contain PK id,
secondary index becomes difficult to keep in memory. To save space UUIDs should be stored as binary(16) values as mentioned https://www.pythian.com/blog/case-auto-increment-mysql
2. Not being ordered

# Approach 3 - Custom key generators
