# Bot Actions

Maana Knowledge Services form a network of GraphQL endpoints, exposing their types, queries, and mutations for direct access, as well as publishing and subscribing to network events.  The Maana platform manages these services, providing authentication, reliable messaging, (automatic) graph persistence (with search and querying), scaling, monitoring, and a rich UX.

Knowledge Services can become Knowledge "Bots" by offering and using "bot actions," which allows the service to interact more directly to users.  Users can configure, start, stop, and schedule bot actions.  Services can report on status and progress for potentially long-running, asynchronous operations, as well as report any errors or messages.

There are two primary scenarios: event handling and direct query or mutation.

Event Handling
--------------

When a Knowledge Service subscribes to and handles an event, such as "fileAdded," it can (optionally) create an instance of a BotAction Kind by mutating the CKG.  As the service performs its operation, it can periodically update the progress (if it is deterministic) and update the status and report errors.

Queries and Mutations
---------------------

A user can invokes a query or mutation either explicitly (e.g., train a classifier on labeled data) or implicitly (i.e., as part of query graph).  In such cases, the service exposes such queries and mutations as returning a BotAction, which indicates to the UI that these are available.

User Experience
---------------

The UI (Explorer, KGE, Context) will query for active BotActions and indicate progress and status on relevant Kinds and instances.  Using stock functionality, service queries and mutations can be connected to inputs and outputs, and static parameters can be set.  Additionally, users can stop/cancel long-running operations, and possibly resume, if the service supports it.

Schema
------

```
enum BotActionStatus {
  PENDING # initial
  IN_PROGRESS
  STOPPING # request to stop processing
  STOPPED # paused, if operation can be resumed, otherwise ERROR (cancelled)
  ERROR # stopped with service-defined or system-defined error(s)
  COMPLETE # stopped without error
}

type BotAction {
  # io.maana.kind
  id: ID! # generated
  # bookkeeping
  created: DateTime! # generated
  lastUpdated: DateTime! # generated
  # disposition
  info: String # human-friendly description
  status: BotActionStatus! # generated (updatable)
  progress: Float # if available, otherwise undetermined
  errors: [JSON!]
  # operation
  service: Service! # who is performing the action
  eventName: String # if in response to an event
  mutation: ServiceMutation # either a mutation
  query: ServiceQuery # or a query
  input: InstanceRef # query/mutation arguments
  output: InstanceRef # query/mutation results
  # lineage
  parent: BotAction
}
```



Sample Query
------------
```
query botAction($id: ID!) {
  botAction(id: $id) {
    name
    service {
      name
    }
    status
    error
  }
```


Helpers
-------

### Node

For [node-based Bots](https://github.com/maana-io/Q-ksvc-templates/tree/master/node), the following helpers have been added to [maana-shared](https://bitbucket.corp.maana.io/projects/H4/repos/h4/browse/ksvcs/packages/maana-shared/src/botAction.js):

```
/**
 * Create a new bot action describing our potentially long-running async operation
 *
 * @param {ApolloClient} client to the GraphQL service
 * @param {CreateBotActionInput} input values for the new BotAction instance
 * @return {ID} identity of the new BotAction instance
 */
export const createBotAction = async (client, input) => { ... }

/**
 * Update the bot action (e.g., progress, status, errors)
 *
 * @param {ApolloClient} client to the GraphQL service
 * @param {UpdateBotActionInput} input values for the existing BotAction instance
 * @return {ID} identity confirmation of the BotAction instance, if updated
 */
export const updateBotAction = async (client, input) => { ... }
```


#### Example

```

import {
  BotActionStatus,
  createBotAction,
  updateBotAction
} from 'io.maana.shared'


export const doAsyncTask = async (client, input) => {
  // Create a BotAction using helper
  const botActionId = await createBotAction(client, {
    serviceId: process.env.SERVICE_ID,
    info: 'task being done asynchronously'
  })


  // Async operation
  const _doAsync = async () => {


    // ...


    // something went wrong
    const confirmId = await updateBotAction(client, {
      id: botActionId,
      status: BotActionStatus.ERROR,
      info: 'task failed'
    })


    // task completed successfully
    const confirmId = await updateBotAction(client, {
      id: botActionId,
      status: BotActionStatus.COMPLETE,
      info: 'task completed'
    })
  }
  _doAsync()


  // Return the bot action id to our caller so they can track the task
  return botActionId
}
```
