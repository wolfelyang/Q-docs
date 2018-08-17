# Development Stages

The development of a Maana Knowledge Application solution can be best described in five stages:

1.  **Design**
2.  **Local Service (Standalone)**
3.  **Local Service  (Maana)**
4.  **Unmanaged Service**
5.  **Managed Service**

## Design Stage

The primary focus of this stage is:

* GraphQL Types (Kinds, Properties, Relations)
* Queries and Mutations (often based on Problem Questions)
* Events (consumed and produced)

During this Design Stage, the overall problem requiring a solution is analyzed, and a domain model is generated with a set of decomposed problem questions. Some discussion of entity sources and data science has taken place, and the point within the Development timeline has been reached where one or more Knowledge Microservices should be coded to provide some new concepts (Types, Kinds) along with Queries, Mutations, and Events that involve them.

### Focus on  GraphQL

For the Knowledge Service/Bot, this is the entire description of (and interface to) their world. Define a file in the GraphQL SDL, such as model.gql, and include custom queries, mutations, and publications/subscriptions.

* Plan the custom resolvers.
* Consider such things as the nature of their core logic.

## Local Service Stage

The primary focus of this stage is:

* Selecting the programming language

* Identifying dependencies - Libraries, existing services and domain models (often based on Problem Questions)

* Core Logic – Used to satisfy GraphQL interface contract

With at least an initial design complete, the following implementation decisions must be made:

* Are there existing Kinds and Services that can be reused?

* Are their existing code libraries or ML models that can be reused?

* What is the best programming language for this task?

* Is there reference data or domain data?

* Are there long-running tasks?

* What are the scale, performance and capacity factors?

Once a programming language is chosen, then an  [existing project template](https://github.com/maana-io/Q-ksvc-templates) can be used to scaffold a new Knowledge Microservice/Bot in Scala/JVM, Python, JavaScript, etc.

**Note:** The development of core logic, or a machine learning solution occurs (as it normally should): writing, testing, and debugging code or improving model accuracy.

The Knowledge Service is a standard GraphQL endpoint, which can be run and tested within the development environment, and used via Maana CLI, its own exposed GraphiQL, a standalone GraphQL Playground, etc.

At this point, the overall problem to be solved has been analyzed and a *domain model* and set of decomposed *problem questions* has been generated, some discussion of *entity sources* and *data science* has taken place, and it is now time to code one or more *Knowledge Microservices* to provide some new *concepts* (types, Kinds) along with *queries*, *mutations*, and *events* that involve them.

## Local Service Stage (Maana)

In the previous stage, it is likely that various calls/services were stubbed or mocked because they required accessing a Maana cluster. It is now time to interact with a Maana deployment, typically one dedicated to development.

Communication to a Maana system requires the use of authentication. This is configured differently, based on the language/framework being used. Refer to the project template documentation used to scaffold the Knowledge service project.

The service being development must be registered with Maana. This is typically done programmatically using the Maana CLI by specifying a *manifest* that describes the service and its dependencies. For example:

```
{
  id: "io.maana.azure.crawler",
  name: "Maana Azure Storage Crawler Service",
  dockerRegistry: null,
  hostedUrl: null,
}
```

The result of registering a new service with Maana is that CKG will generate a dedicated service endpoint for the new service, e.g., https://knowledge.acme.com/service/io.maana.azure.crawler:7331. This enables the standalone service to communicate to Maana, but does not allow Maana to dispatch calls to the standalone service due to network connectivity restrictions. (This will be overcome in the next stages.)

The full GraphQL schema for a service manifest is:

```
# from io.maana.system
type ServiceManifest {
  id: ID!
  name: String
  description: Text
  registeredOn: DateTime
  registeredBy: User
  dockerRegistry: Url
  hostedUrl: Url
  modelSdl: String
}
```
## Unmanaged Service Stage

When it is time to have full participation in the Maana processing network, i.e., having its endpoint services consumed by other services, user interfaces, or automatically based on event subscription, then Maana's CKG must be able to communicate to the service. This means that the service must itself be deployed to a host that is accessible from the Maana cluster, e.g., Heroku, Azure, AWS, on-premise. In this configuration, the service owner is responsible for deploying, monitoring, scaling, securing, etc. the service, since Maana only has knowledge of a GraphQL endpoint URL and the schema it provides.

Note: For more information on this subject please refer to the [Deploy to Heroku Tutorial Page](/tutorials/cli.md)

## Managed Service Stage

Maana can completely manage a **containerized** Knowledge Microservice/Bot, by specifying a Docker registry that the Maana deployment has access to (e.g., DockerHub, Azure). The following additional information is required when configuring a service to be managed by Maana:
