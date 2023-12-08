# References
https://www.youtube.com/watch?v=jLM1nGgsT-I&pp=ygUUaW50ZXJ2aWV3cGVuIGRyb3Bib3g%3D

![](https://github.com/khatwaniNikhil/SystemDesign/blob/main/images/google-drive.png)

# Functional Requirements
1. Users can subscribe/pay to choose data storage limit.
2. subscribed user can upload disparate types of content/media
3. 

# NFR
1. handle network failures during uploads
2. handle high scale of uploads
3. Ensure network bandwidth is efficiently utilised

# Data Transfer 
Chunked transfer
   1. handles network failure driven retries efficiently
   2. for high scale needs, chunks can be uploaded async way using upload queue

# Components
1. Queue: upload queue, upload status notification queue
2. Storage: S3: user uploaded data. DB: users, subscriptions, uploadedcontent_metadata
3. UserMgmtService: onboarding users, Auth
4. RateLimit: fairness of use
5. SusbcriptionMgrService: service handles user payment and allowed data storage limits.
6. IngestionService: horizontally scalable, subscribes to upload queue, crosschecks user crossing thresholds or not with SubscriptionManager service.
7. DataFetchService
8. NotificationService
