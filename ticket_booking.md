# References
https://www.youtube.com/watch?v=lBAwJgoO3Ek

https://www.youtube.com/watch?v=6UAlDIzqhBo

https://astikanand.github.io/techblogs/high-level-system-design/design-bookmyshow

https://akshitbansall.medium.com/db-locking-in-reservation-systems-3b3d574c7676

# Requirements
1. cities served
2. theatres served - seat grid
3. movies/shows served
4. cities, movies, theatres, date-timing, seat availability mapping
5. user can hold the seats while he completes the booking
8. user can make payment & complete booking for a show
9. user can post reviews & comments around movie
10. user receives booking notifications
11. user receives movie/event recommendations based his/her profile
12. waiting customers fairness FIFO

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

## Use case driven right LOCKING strategies
1. user selects the seats and sends a request to the server and yet to complete payment
   1. backend side lock will be acquired for 5–10 minutes, during which either user will pay for the seats or cancel it.
   2. Pessimistic Locking - Single User Booking for a Single Seat
      1. //A single transaction to book a tickets
      2. begin;seatsAvailable() -> Select * from booking where seat_number = ‘A1’ **FOR UPDATE**
      3. bookTickets() -> update booking set customer_id = ‘101’ , seat_status = ‘booked’ where seat_number = ‘A1’;commit;
4. Though not applicable to movie booking, let say concurrent flows where
   1. //Transaction where user-1 books seats A1 to A4
   2. //A single transaction where user-2 tries to book seats A3 to A5 And we want maximum no of seats to be booked if not all
      1. //A single transaction where user-2 tries to book seats A3 to A5
      2. begin;seatsAvailable() -> Select * from booking where seat_number IN  (‘A3’, A4’, ‘A5’) **FOR UPDATE SKIP LOCKED**;
      3. bookTickets() -> update booking set customer_id = ‘102’ , seat_status = ‘booked’ where seat_number IN  (‘A3’, A4’, ‘A5’)commit;

