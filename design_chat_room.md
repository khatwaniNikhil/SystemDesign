# References
https://bytebytego.com/courses/system-design-interview/design-a-chat-system

# Requirements
## Functional
1. 1:1 chat and/or group chat
2. platforms supported: web, mobile etc.
3. scale - 50M DAU, group chat members limit
4. support online/offline status
5. support sending attachments
6. single text message size limits
7. how long chat history storage
8. push notifications

# Design
## Statelful Chat service:
1. to relay messages to online chat participants
2. queue messages in case recipient is offline
3. websockets for bidirectinal persistent connection

## state less services
1. Login/Auth service
2. User profile service
3. Chat group Mgmt. service - REST HTTP endpoints for - chat room creation/delete, add/remove/ban participants
4. Service discovery(Zookeeper): recommend the best chat server for a client based on the criteria like geographical location, server capacity
5. Cleanup service: continous scheduled poll/trigger logic to delete chat room if no of users zero in chat room...delete chat room

## 3rd Party - Push Notification Service
Firebase

## storage
store for User profile, settings/preferences, friends 
Key value store for chat data.
Facebook messenger and Discord use key-value stores. Facebook messenger uses HBase and Discord uses Cassandra/. 

## Data Model
1. Message table for 1 on 1 chat
   1. msg_id
   2. user_id_from
   3. user_id_to
   4. text
   5. create_timestamp
2. Message table for group chat
   1. group_chat_id
   2. user_id
   3. msg_id
   4. text
   5. create_timestamp
3. Msg_id Generator: local unique id generator per chat group(easier to maintain and sufficient for our use case) PREFFERED instead of auto increment or twitter snowfloke. 
   
   
   user chat room enter, banned, exit lifecyle events capture
   user chat room-chats history

   UI - chatroom interface
   show all chats per user since chatroom start


## NFR
1. Runtime allowe capacity configs to be updated and scale up/down infra 
2. Real time communication, Minimal latency.
3. external auth,payments etc.
4. analytics readiness in terms of data capturing
5. persistence - chats and users


chat room as a stateful entity 
		state
		 current set of current users as a state(memory +  disk - ids only)
		 all users of this chat room(disk)
		 all user's chats of this chat room(disk)

		 behaviour
		 add user
		 remove/ban user()
		 newChatMsgFromUser(user)...user specific save to db, broadcasting to current active users, 

		 client side responsibility/state to deliver to some of the peers
		 group of people connected to each other -  able to see each other past messages, broadcast message to all within the group
		 maybe P2P network

		 tricky to implement
		 data security
		 inappropriate messages



assuming 10 chatrooms with 5 users each

for c1 from u1 --msg


API server(stateless)
save to db for c1,u1 ---mysql master
give me list of all active users for c1 ...cache cluster with sharding + replication
router and talking to service mesh
brodcast
	1 async submit to chat room specific queue and then broadcast to the list.....latency/realtime exp.
	2 same request thread broadcasting




1st part
routing
scale

2nd part
p2p networks work in detail
