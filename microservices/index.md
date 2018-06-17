# Single Microservice

A microservice is a single service that is contained in its own container. This service has it's own private data, service, and an API.

![single microservice](https://www.lucidchart.com/publicSegments/view/12806964-89c1-4e0a-8322-4e6ac1b12efa/image.png)

# Multiple Microservices

When dealing with microservices, there are different ways to query data from multiple microservices. There is also the problem of how to commit a transaction against multiple microservices. Some solutions for these problems are:

- API Gateway
- Saga
- Event Sourcing
- Command Query Responsibility Separation (CQRS)

# Multiple Microservices - API Gateway

An API Gateway will query multiple microservices at once and return data combining those results. It also allows a single entry point for managing security.

![multiple microservices - api gateway](https://www.lucidchart.com/publicSegments/view/4d6c388a-1894-4495-814c-617db67d82e9/image.png)

# Multiple Microservices - Saga (Choreography)

Microservices will pass events to each other in a chain to create a transactional process. If anything fails in that chain of events, it will rollback those changes, or update the main processes state (e.g. pending, error, success).

Say you want to create an order. There will be a chain of events when executing the saga:

- Execute OrderSaga
- Send Event CreateOrder
- OrderService receives CreateOrder, creates order and sets status to pending, send PayOrder event
- PaymentService receives PayOrder event, attemps to pay order through payment gateway, Payment fails and event PaymentFailed is sent
- OrderService receives PaymentFailed event, updates order to status of error, sends LogOrderError event
- LogService receives LogOrderError event, logs error

# Multiple Microservices - Saga (Orchestrated)

A single microservice saga will manage the multiple calls out to many microservices. This saga will call, get results, then call another microservice, and over and over till it's done. If there is an issue returned, it will handle accordingly.

- Execute OrderSaga
- Call CreateOrder in OrderService, order is created, returns success
- Call PayOrder in PaymentService, payment fails, returns error
- Call LogOrderError in LogService, log error
- Call UpdateOrder in OrderService, update status to error
- Return failure

# Multiple Microservices - Saga (Choreography) + Event Sourcing

In order for a Saga (Choreagraphy) to be reliable, it needs to publish and subscribe to events. Event Sourcing is one solution to do that.

Event sourcing persists the state of an object such as an Order or a Customer as a sequence of state-changing events. Whenever the state of a business entity changes, a new event is appended to the list of events. The application reconstructs an entity’s current state by replaying the events.

If there are too many events, snapshots can be stored as a quicker way to retrieve objects.

# Multiple Microservices - Command Query Responsibility Segregation (CQRS)

With microservices, queries become more difficult due to the separation between each microservice. API Gateways is one way to solve that, but performance suffers. Another solution is Command Query Responsibility Segregation (CQRS). Basically the commands, PUT, DELETE, and POST are separated from GET requests.

All commands will publish an event to the event store. That event store will then push that event to whatever source is subscribed to it. It will have a view of objects, and information for specific entities. This also allows different entities to have different databases. E.g. if there needs to be a view that is searchable it can be in ElasticSearch. If there is a need for relational data, then it can go in a SQL Server.

![multiple microservices - command query responsibility segregation](https://www.lucidchart.com/publicSegments/view/ab21b4ba-ab17-4679-b8e6-5b7e3c71b6e8/image.png)
