# what is microservice?

## in contrast to monolith

In monolithic services all code is in a single repo. Do for example it can include auth midleware and router that is in front of features. Those features may connect to same database.
Monolith will contain all routing, all middlewares all business logic and all database access to implement all features of our app.

## but what is a microservice?

a single microserivce will contain a routin, middlewares, business logic and database access to implement one single feature of our app.

Microservice is self contained. If every other feature crashes our service still will work. A service should be standalone.

ie:

- service A
  auth middleware -> router -> feature A -> database for A

- service B:
  auth middleware -> router -> feature B -> database for B

## data in microservices

each microservice should have its own database, if it needs one.
Microservies should not share one database. This will introduce single point of failure for them.

## big problem with data

data is 90% of issue in a microservice, distributed system env.
One of the issues we have is when we want for one microservice to get data from other microservices.

We have two strategies of handling communication between microservices: sync and async (no relation with javascript!).

### sync communication

services communicate with each other using direct request. Like rest requests.

#### syn communication pros:

- easy to understand
- if we create a new service that requires data from previous services, we dont have to worry about its database

### sync communication cons:

- it introduces dependencies between our microservices
- if communication between those services fails, the whole communication will fail. Also if our new service needs to communicate with 10 other services, it will be only as fast as the slowest of those requests.
- the dependencies can become very complex in time and it can be hard to understand connection between them.

### async communication

services communicate with each other using events.

method a:
We introduce a concept called a "event bus". Services communicate with eachother via event bus that is like a mux or a router.
This however has similar drawback as sync communication

method b: (most popular one)
this is related to detabase-per-service pattern.

1. We need to define exactly whtat is the purpose of the service. What are the onputs and outputs of the service.
   "given the id of a user, show the title and image for every product they have ever ordered"

2. We create a databse that would serve this goal perfectly. This database will have record of every user that signed up to our application. We just store the rows we need. Each user would have id and ids of products.

3. True challange with this database is that our service needs to be informed on the state of other services. Like when new user gets created, when he orers a new product etc.

To solve this issue, each service will emit a event the instant it happens to them. I.E. when creating a product. The service will emit a event to a event bus. Event bus will then inform all interested services about that fact.

This will allow us to create microservices that are self relient and will work on their own. Only downside is that we duplicate some data across multiple dbs. However as data is cheap, we dont worry about this in context of huge organisations.
