# References
https://www.youtube.com/watch?v=jLM1nGgsT-I&pp=ygUUaW50ZXJ2aWV3cGVuIGRyb3Bib3g%3D

![](https://github.com/khatwaniNikhil/SystemDesign/blob/main/images/google-drive.png)

# Data Transfer
1. For efficient use of bandwidth and handle network (upload/download) failures/retry - chunked transfer
2. In order to handle millions of user's uploads - push user chunks upload to upload queue.

# Microservices
1. Ingestion service - horizontally scalable, subscribes to upload queue.
2. SubscriptionManager service handles user payment and allowed data storage limits.
3. On each upload, IngestionService crosschecks user crossing thresholds or not with SubscriptionManager service.
4. Upload status is again pushed to notification queue.
5. Notification Service subscribes to upload status notifications and pushes the same to user
6. Serving Service takes user requests to fetch respective chunk of data and metada on user request.

# Storage
1. Blobs of data is stored on object storage like S3
2. user file metadata(user, file, chunk, s3_url, created, updated) stored in sharded database with (user,file) based shard key
3. user profile data is again stored in sharded databased with user as shard key.
