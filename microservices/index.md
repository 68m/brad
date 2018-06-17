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


