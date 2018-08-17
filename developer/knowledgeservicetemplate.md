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
