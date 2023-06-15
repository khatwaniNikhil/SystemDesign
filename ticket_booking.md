# References
https://www.youtube.com/watch?v=lBAwJgoO3Ek

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

about each and every individual
component this over here is mobile app
or application and this is the desktop
there can be two different place from
where the users are visiting be book my
show but the same application server
serves the purpose okay so let's talk
about lower balance obviously we need
more balance to keep highly concurrent
system when we are scaling the app
servers horizontally so the load
balancer have different techniques to
balance the load that is you can use
consistent hashing to figure out which
app server the request should hit or you
can go with round-robin where the
requests are done Nick handle
fashion or you can use weighted round
robin or you can also go for least
connection based strategy to do the load
balancing and going forward this here is
the front-end caching system called
varnish it helps you lose the cache
cannot only improve your UX but also
help you to relieve the pressure on the
overall back-end infrastructure over
here also you can use CDN to cache your
pages or api's or any video or image
content and next is the app servers here
you can see there are multiple app
servers over here I mentioned it as app
server so you need to scale these app
servers horizontally to take the
heineken to take the heavy load or to
handle a lot of requests in parallel so
specifically book my show users Java
Spring swagger and hibernate in the App
server but you can always go with Python
based servers or you can also try
node.js for that matter there is no
specific answer that you should use this
language or this particular framework it
all depends on the requirements so the
next is to support such api's for your
to such movies or to such plays or shows
what you can actually do is dump all the
data into elasticsearch over here
elastic search is really good and it is
distributed it has just full search api
is available in the system and it also
can be used as analytics engine which
which which which works on scales as a
half level search engine to answer all
the search queries from the front end so
the next thing you can see is cache
cache is very important to save all the
information related to the movies some
even though in the you know the seat
ordering the place by theater are a lot
of information you can actually use cash
you can use memcached already's whereas
the benefit of using Redis is it is
distributed and persistent so going
forward to the DB you can't just use one
kind of DB that is a DBMS are no sequel
it is always it always works good with
the combination of our DBMS plus almost
equal so what we can actually do is in
case of this particular VMs or ticketing
system we need acid property and
relationship representation because the
relationship will be something like you
have a country's city and city has so
many theaters and each theater can have
multiple screens in it and each screen
has multiple rows of seats and then a
different tier of seeds and many number
of seats right so there is a proper
relationship and also there is a movie
and to be attached to the same same
relationship so we need a proper
relationship to handle these tons of
data right so and also we need to handle
transactions so RDBMS is good since the
data won't grow too much because the
number of theaters and screens of the
seats mostly remain constant upon
addition of new theaters and new places
only the data will increase so it's good
to go with our DB means our DBMS like my
sequel are four strips to save
relational data and also we have tons of
data like movie related information like
from trailer actors through comments and
reviews these are this cannot be saving
our DBMS because this can be considered
as big data or it's too much of data for
our DBMS to digest so it can actually
uses any kind of
or sequel database where it is
distributed by geo like Cassandra you
can go with which is pretty good and
good at holding this kind of big data
like all the comments or the reviews all
the ratings the throughs the actors and
the movie information so I'm tonight I
know it's about what we have to do is
our DBMS also you need to have a
Charlotte data by Geo and also you can
go with either go with master master
slave architecture where slaves can be
used for reading as a read notes and
master can be used for writes that way
you can easily scale a hardy Venus as
well so apart from that we need to have
so now the benefits of using no sequel
also is so since Nord distributed across
geo and we can set the replication
factor and tells you how many copy of
the same data can be saved and also the
consistency level that way you can
always be sure that we won't lose the
data and you know 99.99 percent of
availability and durability also so for
example if these two node goes down
still we have the data in all the other
three nodes going forward let's talk
about the sync workers you can see over
here so why do we need a sink workers so
many times say say for example user has
booked a ticket so we need to generate
the PDF or we need to generate the PNG
of an image or and also we need to send
the emails and SMS or seven tickets via
whatsapp so all these tasks are like
making third party API calls
say for example to send an SMS wait to
make a call to video or to send a email
we need to make a call to
right these are like Network I whoa they
add a lot of latency so in the same API
request for ticket booking we can't
execute all these tasks synchronously so
what we need to do is as soon as the
application server confirms the booking
offer ticket then what they do is they
add a message into the queue that ticket
has been booked now you need to do the
protein or work like sending the ticket
via email SMS or sending the ticket via
whatsapp and also you can do is send a
GCM notifications saying that your six
ticket has been booked successfully so
all this time taking tasks our message
will be added into the queue any workers
which is free will pick up the tasks and
they start executing the time taking
tasks as synchronously so for this part
you can use Python celery which we use
heavily in a lot of distributed
synchronous systems and also there are
similar kind of distributed as
synchronous workers available in Java go
on etc so you can use any of that all
works well so moving forward let's talk
about business intelligence our
recommendation engine which we spoke in
the feech you know in the list of the
features so to do that we need to
obviously have a platform to get the
business intelligence so what we do is
all the lots all the user activities all
the information can be dumped into
Hadoop HDFS as a law dump of log file or
something then on top of it we can run
big or high queries to extract the
information like like user behavior or
use a graph or
so if you can plane our machine learning
model using the same data to free to do
recommend movies for the customers are
the users so in in the meanwhile we can
use say something like Kafka in between
Kafka queue in between tough got you in
between and tweet the data into spark to
do something like real-time analysis we
can either use spark or storm over here
to get the trends or latest trends or
what's happening in the system and also
to understand the user activity and
behavior and also sometimes we can do
figure out fraud detection and
mitigation strategy using this park and
storm stream processing engine so that
way as yeah that that's about it so
moving forward what we can do is here
you can say you can see that there is a
payment gateway this we can go with a
third-party payment gateway like for
example just BMS users just pay which
has a lot of different vendors credit
cards banking application banking banks
and all the different wallets so we can
just so when the when there is a ticket
request to book a seat so we can rewrite
to the payment gateway and once the
payment is confirmed we can issue the
tickets using a synchronous worker and a
copy of tickets in the synchronous API
called also now let's talk about the
working steps what happens when a
customer comes to BMS and books a ticket
so the first step what he is going to do
is customer comes into he uses the
application he does the location filter
and sorting and going into the movies to
the specific movie so to find the
location what we can do
is if it is a mobile phone we can use
the GPS location available to the mobile
phone or if it is a laptop we can use
the ISP Internet service provider
there's IP based location strategy to
figure out the location of the user and
we can set all the nearby theaters and
the movies which is released in that
particular area so then that when the
user selects a movie he checks the
different timings available in that
theater and the next thing what he does
is opens particular timings for for
theater and then he sees all the
available and unavailable are booked
tickets for seats now what VMs should do
is should also lock the seat for the
next ten minutes what it does is VMs
system makes a call to the theater which
which I showed you in the very beginning
of the video it makes a call to the
theater DB to block the seats so now the
ticket is booked temporarily to our user
trying to book so every other app in the
theater or other system or the other
aggregator can't book the same ticket
now the user can proceed to booked the
ticket are the ticket or the seat will
be reopened if he didn't book it after
10 or 15 minutes now what it does is if
the user continues to work he will check
the invoice with a payment option using
the payment gateway over here and then
the request will be processed and then a
callback will be happen to the app
server saying the payment is processed
and successful so once the payment is
successful the theater who gives you a
unique ID and that will be received in
the app server so what what BMS does is
using that unique ID a ticket will be
generated you are code which is attached
to the gate and
a copy of ticket will be immediately
shown to the user and also a message to
the queue will be added to send the same
copy of tickets with the information
like date/time play is a QR code and the
address of the theatre and everything
will be sent via email SMS all the
tickets so now what happens is when a
customer goes to the theatre he shows
the QR code and the same ID will be also
saved with the theater so then both
match the ticket ID and then the user
enters the theatre so yeah that's the
overflow of how user interacts with the
system BMS is built entirely on Amazon
platform it uses micro service
architecture like every component is
separated out and they run independent
of they are not monolithic architecture
so all these app servers are and the
workers are hosted on ec2 instances of
Amazon and the sealian is cloud fair and
more all the trailers gallery are saved
in s3 of its own
but my shows UI is built on top of
react.js
bootstrap J's and CSS the server they
use is Chavez spring boot swagger and
hibernate so the database which they use
is my sequel and the server or I haven't
shown here is used Tomcat for the
caching they have used hazel cast for
all kind of distributed system there
they are using traffic and cue over here
and the payment gateway they are using
just pay for the deployment of the whole
different micro services they use docker
and ansible and along with that they get
for to maintain the core base for
logging collection and monitoring what
the do is all the logs are pushed to log
stash and locks - can read from syslog
lock file or queue and everything all
the logs will be sent to log stash and
locks - based on a lot of filtering
conditions will be redirected to l stack
or does a lot of different actions like
it can send a notification yr HipChat
whatsapp or it can even raise Jitterbug
based on filter the logs will be
redirected to ELQ in here on cabana we
can build a dashboard to see how what is
the error rate and etc so hard from that
the lower balance which they use is
nginx and that's it let's see what are
the tables which we need it for the
system design which we just saw so
obviously we need a table with place the
same table can be used to save the
hierarchical data for any given theatres
like country state City and the street
where the the theater is so that we want
to many relationship and every theater
will have multiple screens in it so we
need to have one to meditation ship
between theater screen and each screen
will also have different tiers of seats
or different kind
rows of seats opening multiple deer and
then every tear will have different
seats so the pricing for different tier
of seats varies so this is where we can
handle that
so each tier will have multiple seats so
also only one movie can be played in a
particular screen at any given part of
the time so there will be only one to
one relationship between screen and a
movie we also need our first stage our
first table that contains all the
different offers available for a
particular movie or it can be for a
particular screen or it can be a for a
particular seats and also we here we
need to have a user table which contains
user information and if one user can
book multiple tickets and each ticket
can have multiple seats in it so this is
the high level database table structure
here are the list of tables which we
need in no sequel database the tables
depends on the queries in no sequel we
need to anticipate all the different
queries and we have to do the data
modelling based on the number of queries
which we are going to support in the
system so and also you can see that
there are no relationship between the
tables as in no sequel we can't have we
can to join and we can't meet in the
relationship and here are the high level
did tables which we need to have in no
sequel like we need a table to save all
the comments for a given movie and we
need a table to save all the ratings for
a given movie and also we need a table
to save information about the movie like
description and synopsis etc and we need
a table to save all the video and audio
information or the path to the images
and the videos of a given movie and we
need a table to save artists related
information and we need a table to save
cast and crew and we need a table to
save all the reviews about the movie and
we need a lot of many different tables
to save analytics data which will be
consumed by the back-end team system
design is not simple
system design is subjective it depends
on the requirements features it depends
on the location kind of user base and
etc so there is no such thing as ideal
system design for a particular system
system design evolves by time and it
gets optimized and tweaked so if you
guys like this video a power system
design for book my show or ticket
booking platform please do subscribe to
my channel and leave a comment and share
this video with your friends thanks a
lot
