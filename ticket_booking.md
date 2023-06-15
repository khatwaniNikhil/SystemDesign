# References
https://www.youtube.com/watch?v=lBAwJgoO3Ek

https://www.youtube.com/watch?v=6UAlDIzqhBo

https://astikanand.github.io/techblogs/high-level-system-design/design-bookmyshow

# Requirements
1. user can see the cities where booking platform is serving
2. user can see the movies with live shows in that city
3. user can see the city->movie->theatres with live shows
4. user can see the city->movie->theatres->show timings 
5. user can see the city->movie->theatres->show timing -> seating grid and availability
6. user can hold the seats while he completes the booking
7. user can make payment & complete booking for a show
8. user can post reviews & comments around movie
9. user receives booking notifications
10. user receives movie/event recommendations based his/her profile
11. waiting customers fairness FIFO

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
![](https://github.com/khatwaniNikhil/SystemDesign/blob/main/images/ticket_booking_api_design.png)

