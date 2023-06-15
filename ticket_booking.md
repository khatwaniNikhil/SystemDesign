# References
https://www.youtube.com/watch?v=lBAwJgoO3Ek
https://www.youtube.com/watch?v=6UAlDIzqhBo

# Requirements
1. user can see the latest status of ticket availability 
2. user can make payment & complete booking
3. user can pos reviews & comments
4. user receives booking notifications
5. user receives movie/event recommnedations based his/her profile

# NFR
1. high scale of requests - booking, reading reviews etc. (90k bookings in a day)
2. responsive UI - mobile, web experience

# Impl - Thoughts 
1. for responsive UI - avoid multiple clicks in quick succession
2. while user is searching for movie name - don't make hit to backend on each char entered(threshold no. of chars)
3. Different data storage layers: 
   1. NoSQL for Movie metadata and reviews
   2. SQL - booking transactions and registered users profile
5. reverproxy cache for assets caching
6. CDN if multiple geography driven customers
7. Async queue based worked for user booking notifications
8. hadaop + apache spark based infra for movie recommendations and other BI
9. integrate with third party PG(handles all complexities behind the scenes)

# HLD 
https://github.com/khatwaniNikhil/SystemDesign/blob/main/images/booking_design_hld.pdf

# Data Modelling
![](https://github.com/khatwaniNikhil/SystemDesign/blob/main/images/ticketing_lld_1.png)
![](https://github.com/khatwaniNikhil/SystemDesign/blob/main/images/ticketing_lld_2.png)

# API 
![]()

