# References
https://www.youtube.com/watch?v=R_agd5qZ26Y&list=PLPkuArhPxxQGkbl-_STo8FFxBBB4ri-tl&index=1

# Requirements
![](https://github.com/khatwaniNikhil/SystemDesign/blob/main/images/Screenshot%20(36).png)
![](https://github.com/khatwaniNikhil/SystemDesign/blob/main/images/Screenshot%20(38).png)

# Arch. Diagram
![](https://github.com/khatwaniNikhil/SystemDesign/blob/main/images/Screenshot%20(39).png)

# Scalable Real time commn across multiple microservices
event bus like kafka with subscription by microservices.

# Real time commn across drivers and riders
1. load balanced cluster of driver and rider api's
2. maintain websockets persistent connection of driver and rider with there respective api's
3. driver and rider api commn with each other to trigger notifications for rider/driver.

# Database design
1. high writes(based on driver's/ride journey) to db 
2. driver, rider and trip tables 
3. should be horizontally scalable via sharding - 
4. driver table(driverid as sharing key), rider table(riderid as sharding key), trip(tripid as sharding key, riderid, driverid, status)
5. trips table will have diff. access patterns around lookup trips by rider or driver, so separated sharded indexes over riderid and driverid with status stored in sorted format for quick access by specific status

# Delegated payments
auth + delegate to stripe which invokes webhook which pushes an event in kafka event bus picked up by microservices to proceed with the trip

# Map 
integrate google maps/mapbox, geoencoding street add to lat/long., get diretions for driver to reach rider location.

# Ride Pricing
demand driven dynamic pricing, demand calculated by stream processing ride requests at given location in sliding window via kafka-spark and stored in redis.
pricing service refers to demand via redis in realtime and calculate dynamic price.

# riders/Trip service 
1. efficiently find drivers nearby:
2. geospatial indexing(uber designed h3 algo.) - group rides by cell
3. using cell id(calculated via lat/long).
4. once cell id can be used to calculate other nearby cells to find drivers nearby
5. calculate ETA

# e2e flow
1. rider uses map service to specify his location and specify destination and request ride
2. pricing service subscribe event to calculate dynamic nprice basis current demand and rider starting and desitination
3. rider service subscribe to event of rider requesting a ride
4. find closest driver
5. inform driver to accept or reject
6. on accept, notifiy rider with driver details, driver get directions to rider location
7. on decline, repeat for another nearby driver
	
	
		



