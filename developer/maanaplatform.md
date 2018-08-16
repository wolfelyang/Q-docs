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
