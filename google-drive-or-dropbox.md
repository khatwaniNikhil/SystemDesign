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
   3. efficient bandwidth utilisation: let say user watching a video but might not watch whole vidoe so loading immediate next chunks on need basis is efficient.
   4. Better user exp. - no need to wait for complete file to be downloaded/uploaded

# Components
1. Queue: upload queue, upload status notification queue
2. Storage:
    1. S3: user uploaded data.
    2. DB: users, subscriptions, uploaded_content_metadata
4. UserMgmtService: onboarding users, Auth
5. RateLimit: fairness of use
6. SusbcriptionMgrService: service handles user payment and allowed data storage limits.
7. IngestionService: horizontally scalable, subscribes to upload queue, crosschecks user crossing thresholds or not with SubscriptionManager service.
8. DataFetchService
9. NotificationService
