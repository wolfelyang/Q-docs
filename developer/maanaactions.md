# Maana Actions (?)

The following section offers greater detail on the activities associated with Maana Knowlege Solutions.

## Defining a Model in Maana

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
*	How to create a graph from a GQL definition
  * [Create a Graph from a GQL Definition](developer/whitepaper.md) 
*	How to add instance data
*	How to query instance data
*	How to perform complex queries (https://confluence.corp.maana.io/display/RD/Technical+Design+Notes#TechnicalDesignNotes-Queries)
*	How to perform search (https://confluence.corp.maana.io/display/RD/Maana+Q+Search+-+Technical+Design)
