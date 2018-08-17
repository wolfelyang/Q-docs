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
