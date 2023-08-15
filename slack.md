# References
https://www.infoq.com/presentations/slack-infrastructure/?utm_source=youtube&utm_medium=link&utm_campaign=qcontalks#downloadPdf](https://www.youtube.com/watch?v=WE9c9AZe-DY)
[QConSF2016-KeithAdams-HowSlackWorks.pdf](https://github.com/rohit0286/SystemDesign/files/12341123/QConSF2016-KeithAdams-HowSlackWorks.pdf)

# Primary use case
persistent group messaging 
team admin based imports & exports
user while typing message opening a twitter user landing url and user profile pick is shown within few secs along with other metadata.

# Scale 
    Millions of connected user(websocket connections, not billions
    avg. user activity
        actively using for 2hrs on week days
        connected for 10hrs a week day

# User journey
## Initiate session 
1. uses slack client to hit webapp to initiate session(rtm.start (realtime message commn to start)) with token passed for auth
2. slack usign PHP scaled out LAMP stack monolith for this webapp(memcache wrapped around sharded mysql)
3. POST /api/rtm.start?token=
4. http response acks the session request and shared web socket url
5. offset pointer in terms of from where to pick unread messages is returned as url param
6. message server maintains a in memory buffer of recent messages of last 30 seconds. 


### eventual consistent snapshot 
1. since using master master with writes on both nodes.
2. all updates post the state of world last time we connected are shared with the user at the team level

## Broadcasting - Message server
1. takes care of whats happening within team to be broadcasted to all connected team members
2. side car to a different server for persistence - push the message so that currently not active members can receive in future.

# Data model
1. team's data is stored in normalised form in relational db - teams, users, channels, messages, comments
2. sharing by team
   1. instead hash+mod based strategy, lookup to shards from a team is kept arbitary(persisted in db) for administrative reasons so that if a shard gets hot we can split it manually or automatically.
   2. domain to team to shard mapping is stored in db
  
## Replication
1. across DC replication
2. master master replication topology
   
### write conflicts 
1. customised strategies to handle conflicts be eventually consistent
2. insert on duplicate key update
3. able to quickly read what u write managed via even team id's(lowered a bit of your team ID) routed to same master and odd team id's routed to even master
4. scaling this is difficult and wip to find ways to better handle the same

# Fault tolerance - Network issues
persistence happening across data center boundary

# Deferred - Job queue + workers
1. for exports and imports
2. for url based lookups,for example - user while typing message opening a twitter user landing url

# Infra 
1. team based partitioning
   1. easy scaling
   2. natural fit for paid product
   3. isolate failures and perf problems
   4. customer complaints are localised
2. per team message server for low latency broadcasting 

