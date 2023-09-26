1. inter process commn similar to email where two parties never talk directly but via mail box/queue.
2. inter process commn where one party want to keep producing more messages/events for other parties without waiting for processing of any of the existing messages. Restuarant front worker keep accepting new orders irrespective of past orders status which r delegated to kitchen.
3. too many requests leading to timeouts
4. polling a datastore too often
5. scale up/down during peak hours
6. For long-running processes and background jobs:
	sending emails/notifications
	image scaling, video encoding, pdf processing, filescanning
7. middleman in between microservices 
