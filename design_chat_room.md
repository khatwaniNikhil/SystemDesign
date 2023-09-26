# Requirements
## Functional
1. create a new chat room with max capacity of people
  1.1 consider allowed capacity of no. of chat rooms before accepting new chat room request, startup config: chat rooms capacity, single chat room max people capacity	

2. validate and enter a user to a chat room
 
3. continous scheduled poll/trigger logic to delete chat room if no of users zero in chat room...delete chat room

4. storage
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


multiplexing at backend to resue existing connections

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
