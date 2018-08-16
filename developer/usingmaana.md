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
* [How to Create a Graph from a GQL Definition](https://qtesting.gitbook.io/qtesting/developer/usingmaana/creategqlgraph)
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
