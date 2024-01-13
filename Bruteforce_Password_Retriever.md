# Assumption: don’t think about rate limiting and security
![](https://github.com/khatwaniNikhil/SystemDesign/blob/main/images/Bruteforce_Password_Retriever.jpeg)

# Server side capabilities
1. Next Batch of potentical passwords (to check) generator with last batch id as input. In-memory generation.  
2. Batch jobs Manager
    1. status tracker
    2. persist in some db
    3. Healtchcheck
    4. Re trigger failed jobs
    5. Handle worker notifications 
    6. Send poison signal to current running workers and update in db if actual password found
3. Batch passwork check worker:
   1. can work on one batch at a time
   2. Integrate with twitter

# What to run on FREE COMPUTE RESOURCES versus Private cloud
