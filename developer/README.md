---
description: Knowledge Technology - Developer Guide
---


# Introduction

### Welcome to Maana

Maana was founded with the vision of using technology to systematize the world’s industrial expertise and data into digital knowledge that could significantly advance the global economy. Our mission is to facilitate the tens of millions of experts working in industrial companies around the world, and give them the ability to make better decisions, faster. We are devoted to putting the power of artificial intelligence \(AI\) into the hands of millions of industry experts, offering enhanced decision-making tools in a rapid response environment.

### Better and Faster

In 2013 Maana invented a new way to represent industrial knowledge mathematically, using the patented Maana Computational Knowledge Graph™. This unique technology enables industrial companies to encode human expertise and data from across silos into digital knowledge, eliminating the need to move data and enabling the creation of thousands of models at scale, through the re-usability of models across the enterprise.

### Scope

This guide is intended for the use of Solution Developers and Data Scientists, and describes how to develop such services \(aka "bots"\) and applications and operationalize them in a production setting.

### Glossary

Prior to working with the Maana Platform, it is suggested that you familiarize yourself with some basic terms and concepts that will help the make most out of your Maana portal experience. For a complete Glossary of Terms, please refer to the Appendix found at the back of this document, or go to the [Glossary](/glossary.md)  section of the Maana Corporate site.


# Document Structure:

    * [The Maana Platform](developer/maanaplatform.md)
    * [Knowledge Microservices](developer/knowledgemicroservices.md)
        * [Bot Actions](developer/botactions.md)
    * [Development Stages](developer/developmentstages.md)
    * [Knowledge Applications](developer/knowledgeapplications.md)
    * [Using Maana](developer/usingmaana.md)
        * [Create a Graph from a GQL Definition](developer/creategqlgraph.md)
    * [Developing Knowledge Microservices From Maana Service Templates](developer/knowledgeservicetemplate.md)
    * [Troubleshooting](developer/troubleshooting.md)



# The Maana Platform

The Maana platform is used by solutions teams to deliver knowledge applications to their end users \(i.e., business users, SMEs, managers\). This “solution team” approach involves the collaboration of analysts, engineers, and data scientists. The platform is designed to build an enterprise-wide knowledge layer, utilizing search and exploration for solution development and delivery.

### The Computational Knowledge Graph

At the core of any Maana solution sits our Computational Knowledge Graph \(CKG\), a network of models built using machine learning techniques and artificial intelligence that powers AI-driven applications used to digitize decision support and operations. It allows the knowledge of the business to be incrementally captured and grown, coupling that with ongoing evolution and ever-increasing sophistication as more projects are developed.

### Utillizing GraphQL

Maana uses GraphQL to represent and expose its Computational Knowledge Graph. GraphQL is a data query language created by Facebook and open-sourced in 2015 as an alternative to REST interfaces.

GraphQL provides a complete and understandable description of the data in the API, gives clients the power to ask for exactly what they need and nothing more. This adaptable language makes it easier to evolve APIs over time, and enables powerful developer tools. With the GraphQL type system, the developers can access the full capabilities of your data from a single endpoint.

GraphQL creates a uniform API across your entire application without being limited by a specific storage engine. The developer provides functions for each field in the type system, and GraphQL calls them with optimal concurrency.

## Maana Platform Architecture and GraphQL

For more information on using on Graph QL, please refer to the link provided below:

<IMAGE>

* [How to GraphQL](https://www.howtographql.com/)

## Computational Knowledge Graph Features

With the Computational Knowledge Graph:

* **The Graph is dynamic** - Nodes, which represent concepts in the graph, are not static containers, but rather act as computational vessels, allowing algorithms to be stored and executed.

* **Reusable Models** - Knowledge Graph flexibility enables groups across the organization to leverage and build-upon models created by other groups, dramatically accelerating the speed at which models are created throughout the organization. These models are dynamic, and once operationalized into applications, they learn and adapt based on the user behaviors and provide continuous intelligence for day-to-day operations.

* **The structure of data** -  Is separated from its contents enabling a fluidity of modeling,  data from any source or format can be seamlessly integrated, modeled, searched, analyzed, operationalized and re-purposed.

* **Data remains at the source** - Only the most relevant data, within the context of what is being optimized, is indexed and brought into the graph.

* **Resulting Data Models** - Each is a unique combination of three key components, which are instrumental in optimizing assets and decision flows:

      1.  Subject Matter Expertise
      2.  Relevant Silo Data
      3.  The Right Algorithm

**Note**:  These algorithms might range from being as simple as pulling in new external source data, to as complicated as classification of documents through machine learning.

The CKG consists of **concepts** and **properties**, **instances** and **values**, and **relations** and **links**.

Consider the concept of a _ContainerShip_ with the properties of _name_, _length_, _position_, etc. A specific instance \(entity\) for our ContainerShip might have values for each of these properties - such as the vessel Maersk Viking having a length of _400 meters._ Such properties can be "scale-able" \(e.g., numbers, strings, dates\) or might refer to other concepts/instances - such as the _Hold Cargo_ of the vessel.

In some cases, property values for an instance may simply be stored, as they are rarely subject to change. In other cases, they will be dynamically computed, due to constantly changing values - such as a ship's _weight_ \(which is dependent upon its cargo\) or variables like the vessel’s _current position_ \(which requires getting a GPS reading\).

Unlike a traditional graph database, Maana incorporates arbitrary computation \(through custom GraphQL resolvers\) and distributes the graph into subgraphs managed by different microservices, optionally with their own dedicated persistence mechanism. This separation enables a fluidity of modeling, permitting data from any source and in any format to be seamlessly integrated, modeled, searched, analyzed, operationalized and re-purposed. It also places more responsibility on the microservices to provide their own storage.

## Data Model Split

To address this, Maana proposes an explicit split between the data models \(i.e., GraphQL type definitions\) that a service uses and its operations \(i.e., GraphQL resolvers\). Maana will generate the appropriate managed service for such models using KindDB, Prisma, neo4j, etc. With this, the solution developer only provides the logic they care about and let the system take care of all the CRUD/ORM-like operations on the data.

# Knowledge Microservices

Knowledge microservices are a class of GraphQL services that are developed for the Maana platform. Unlike pure client-server or n-tier architectures, Maana's microservices act as peers in an asynchronous and loosely-coupled arrangement that promotes independent scaling and extensibility. They provide identity and access controls, graph persistence, search, machine learning, and natural language processing.

As a consequence, these peer microservices provide reasoning capabilities to Knowledge Applications, which help solve domain-specific problems and support optimal decision-making that is capable of learning over time. Taken together, these services allow the solution developer to focus on designing GraphQL schemas and implementing computational resolvers only where needed.

The Maana platform manages these services, providing authentication, reliable messaging, \(automatic\) graph persistence \(with search and querying\), scaling, monitoring, and a rich UX. These GraphQL services include:

* Authenticated Access
* Client/Server Boilerplate
* Reliable Messaging using RabbitMQ
* Lifecycle Management \(Info, Register, Deregister\)
* Docker Containerization and Automatic Scaling/Load Balancing

Maana Knowledge Services form a network of GraphQL endpoints, exposing their types, queries, and mutations for direct access, as well as publishing and subscribing to network events. A GraphQL service \(or endpoint\) consists of:

* Types
* Queries
* Mutations
* Events \(pub/sub\)

Examples of Knowledge Microservices include:
| Indexers | Miners | Classifiers |
| ----- | ----- | ----- |
| Text | Statistics | Entity |
| Number | Probabilities | Field |
| Time | NER/NLP  | Document |
| Geospatial |  | Image|
| Geometric | |  |

## Bots and Bot Actions

A *Bot* is a Knowledge Microservice that “listens” for specific events on a system bus, and acts automatically when such events happen to mine and enrich the graph. They provide specialized queries and mutations that perform *BotActions*, allowing the bot to provide asynchronous status updates.

Example:  When a raw data file is loaded into MAANA, a bot automatically analyzes it to identify mentions of entities like persons, phone numbers, values, and facts.

Users can configure, start, stop, and schedule bot actions. This enables user interface components to immediately return the latest status:
* Throughout the duration of extended, ongoing operations.
* By events that act as automatic triggers (such as entity recognition, new concept creation and classification).
* Report any errors or messages.

There are two primary scenarios to consider here:

 1. Event Handling
 2. Direct Query/Mutations

### Event Handling

When a Knowledge Service subscribes to and handles an event, such as "fileAdded," it can (optionally) create an instance of a BotAction Kind by mutating the CKG. As the service performs its operation, it can periodically update the progress (if it is deterministic) and update the status and report errors.

### Queries and Mutations

A user can invoke a query or mutation either explicitly (e.g., train a classifier on labeled data) or implicitly (i.e., as part of query graph). In such cases, the service exposes such queries and mutations as returning a BotAction.

**Note:** For more information on bots and bot actions including UI experience asynchronous results, schema, sample queries, helpers, ect.  Please refer to the bot actions page [the Bot Actions Page.](/developer/botactions.md)

## Kinds

All Kinds (concepts, types) are associated with a service. This service is said to provide the (entities of) the Kind. Many Kinds are purely extensional (i.e., data) and do not have custom CRUD behavior. These Kinds are automatically managed by the Computational Knowledge Graph (CKG) and stored in the KindDB, where they are indexed for efficient search and querying (including sophisticated entity concurrences).

Services also depend on existing Kinds and queries, mutations, and events. The services that provide these Kinds (which may be fully managed by CKG/KindDB) can be imported into a new service purely through GraphQL and specified in a manifest that is used to create and register a new service. This process will result in a merged schema on a service-specific endpoint that the newly developed service uses for all Maana GraphQL communication (non-pub/sub).

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

# Knowledge Applications

Solution developers can develop knoweldge applications on top of the MAANA knwoledge graph.

* Who is the audience for the applications?
  * SME and are looking for answers to specific questions \(i.e. Given a ship, a ship route and an omitted port, what re-route options can be considered to minimize trip duration for the loads onboard?\)
* What type of applications can be developed?
  * Custom web apps, Power BI apps, Tableau, Spotfire? others?
* What are the steps to build an application?
* How does a developer publish and manage/share/update/delete an app?


# Using Maana (?)

The following section offers greater detail on the activities associated with Maana Knowlege Solutions.

# Defining a Model in Maana

## Model Definition

The following documentation uses **XXX.XXX.XXX.XXX** facing to represent the public URL of the system. All of the following examples use the GraphiQL endpoint - which is interactive, programmatic access conducted through the GraphQL endpoint.  GraphQL API is organized around three main building blocks:

1. Schema

2. Queries

3. Resolvers

Queries can be executed via GET or POST, with either the body of the query as the query string or in the body of the POST. All mutations must use the POST endpoint and have the header content type set to application/json. Also, the GraphQL server needs a resolver to know what to do with an incoming query.

![](/Users/tmelhuish/Documents/testing/docs/assets/animations/graphqlQuery.png)
Source : [Anatomy of a GraphQL Query](https://blog.apollographql.com/the-anatomy-of-a-graphql-query-6dffa9e9e747)

The following is an example of how to create a graph with no mutation specified, using the cURL command line tool for getting or sending files using URL syntax.

```
curl -v -H "Content-Type: application/json" -X POST -d "{\"query\": \"mutation { ..... }\"}" http://XXX.XXX.XXX.XXX:8003/graphql
```

Maana engineering team is currently implementing authentication functionality, which will be required for all clients.
*	How to Create a Graph from a GQL Definition (https://confluence.corp.maana.io/display/RD/Maana+Q+programmatic+interface)
*	How to add Instance Data
*	How to query Instance Data
*	How to perform Complex Queries (https://confluence.corp.maana.io/display/RD/Technical+Design+Notes#TechnicalDesignNotes-Queries)
*	How to perform Search (https://confluence.corp.maana.io/display/RD/Maana+Q+Search+-+Technical+Design)

## Hydrating the Model with Data

*	Create a Service with graphql schema. Types must correspond to Kinds on the portal endpoint XXX.XXX.XXX.XXX:8003/graphiql. Note that the service can have many Kind definitions.

```
mutation {
  addServiceSource(input: {
    name: "TestService",
    schema: "type TestKind { \n id: ID \n name: String! \n something: Int! }"
  })
}
```
* Once the Service has been created, it will return a Service ID:

```
{
  "data": {
    "addServiceSource": "54769aae-2c63-4415-9b34-74e9ceb1d789"
  }
}
```

This ID is used to establish the Service endpoint for subsequent interactions.

> XXX.XXX.XXX.XXX:8003/service/54769aae-2c63-4415-9b34-74e9ceb1d789/graphiql

**Note:** If the service is re-created by updating definitions, its service ID will change, while the old service will still be available.

## Adding a Single Instance of the Kind

```
mutation {
  addTestKind(input: {
    name: "testname",
    something: 42
  })
}
```
* An ID is assigned and returned for the instance itself

```
{
  "data": {
    "addTestKind": "431f9338-95be-4f89-968c-4f2bb197965c"
  }
}
```
* Once the instance data was added, we can query it using:


```
query {
  allTestKinds {
    id
    name
    something
  }
}
```
## Adding Multiple Data Instances simultaneously to a kind

```
mutation addSomethings {
  addTestKinds(input: [{
    name: "testname2",
    something: 45
  }, {
    name: "testname3",
    something: 43
  }
  ])
}
```
**Note:** Larger data volumes should be broken into reasonably sized chunks (i.e. up to 5,000 instances), and submitted sequentially through the endpoint.

* Data can be queried using:
```
query {
  allTestKinds {
    id
    name
    something
  }
}
```


# Querying a model

## Query Though Service End Points

The most common way to query the data once ingested in the graph:

```
query {
  allTestKinds {
    id
    name
    something
  }
}
```

## Query using Global Entry Points

There are more generic ways to read Kinds from a model than through service entry points.  However,  when using the Global Entry Points (rather than the Service Entry Points), Kinds should be referenced by ID.

**Note:**  The IDs for each Kind can be obtained from the Service ID by running a query against XXX.XXX.XXX.XXX:8003

* Here is an example on how to obtain an ID for a Kind from the Service ID:

```
query foo {
  serviceSource(id:"54769aae-2c63-4415-9b34-74e9ceb1d789") {
    name
    kinds {
      name
      id
    }
  }
}
```
* The query above should return:

```
{
  "data": {
    "serviceSource": {
      "name": "TestService",
      "kinds": [
        {
          "name": "54769aae-2c63-4415-9b34-74e9ceb1d789_TestKind",
          "id": "a745e4d7-74e9-4023-8f39-bc63d6076326"
        }
      ]
    }
  }
}
```

## Query using Allinstances

This type of query can be used to get instances for a kind in the same format as the query entry point:

```
query {
  allInstances(tenantId:0, kindId:"a745e4d7-74e9-4023-8f39-bc63d6076326") {
    records {
      STRING
      ID
    }
  }
}
```

## Query across Kinds

More complex queries can be used to more efficiently query across Kinds. Many internal services can use this endpoint to reduce the number of DB requests required to fulfil a request.

```
query {
    kindDBQuery(kindQuery:{
      kindId:"fcd39416-337c-4566-b5bf-56cc8ff725d2"
      fieldFilters: [{
        op: "=="
        fieldName: "State"
        value: {
          STRING:"WA"
        }
      }]
      and: [{
        kindId:"fcd39416-337c-4566-b5bf-56cc8ff725d2"
        toFieldName:"City"
        fromFieldName: "City"
        fieldFilters:[{
          op: "=="
          fieldName: "Zipcode"
          value: {
            STRING: "98053"
          }
        }]
      }]
    }) {
      records {
        ID
        STRING
        INT
        FLOAT
    }
  }
}
```
The outer most kind is the type returned, while inner Kinds form a DAG where each inner kind constrains its outer Kinds instances. The DAG should be considered to be a graph walk, where constraints are applied at each step in the walk, data is returned only if all constraints are true.

Filters are a collection of {op, fieldname, value} triples that constrain the Kind on which they are applied.
Operators that can be used:
*	for Strings : "==" , "!=" , "oneof"
*	for Numbers (Int and Float) :  "<", "<=", ">", ">="

# Developing Knowledge Microservices Utillizing Maana Knnowledge Service Templates

## Project Setup

To begin a project setup, begin by selecting a preferred language, and follow the instructions provided in the following sections for customization.


## Maana Knowledge Service Templates

In an effort to maintain best practices, encourage code base coherence, and minimize maintenance issues, standardized procedures are offered for a number of things that a productive developer should not have to worry about and instead focus on adding real features.

Every project should follow a common, consistent pattern to reduce the tax imposed by infrastructure, such as web services and middleware, GraphQL services, logging, build, deployment, etc. We recommend always cloning a new project from the Maana Knowledge Service template for your preferred language.  Once cloned, perform the customizations recommended in the template's README.md.

Note:  If your language isn't represented, we'd be happy to work with you to develop a template -  or simply send a pull request to have it included.

## Development

Steps for Deployment:

1. Define the GraphQL schema for the Service Endpoint. (HOW)
2. Implement the appropriate Resolvers. (HOW)
3. Be the client (Query, Mutate, Subscribe) to other Knowledge Services (Dependencies). (HOW)
4. Stitch together existing Services, providing an aggregated Endpoint (to simplify client access).(HOW)

## Packaging

Steps for Packaging:

1. Prepare Deployment
2. For a managed service: Create a Docker container

Note:  Unmanaged Services are not covered by Maana.   (WHAT IS NOT COVERED)

## Addition of a Knowledge Microservice to the MAANA Catalog

To add a Knowledge Microservice in the Maana Catalog:

1. Prepare a Service Manifest (basically a serialized instance of Kind Service).

2. Register the Service in the Catalog.

At this point, Maana will:

* Examine the Service.
* Create the boilerplate for Managed Kinds, including:
  * Types
  * Queries
  * Mutations
  * Subscriptions
* Register with CGK
* Boilerplate:
  * Queries
  * Mutation Resolvers
  * Event Emitters
* Stitch Resolver schema (Synthetic to Service-provided)

# Troubleshooting

## Development Scenarios
Local:
Configure and run a local Docker machine Maana system and custom services (i.e., a "deployment").

Maana R&D Activities:
Remote: Mixed Environments - A Maana deployment is hosted elsewhere (e.g., Azure), developing a service locally.

Not supported in Version 3.0?

If local and developing an a (locally) deployed service, then "docker stop" it and run/debug it manually.
