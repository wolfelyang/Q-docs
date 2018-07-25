Research and Development : Developer Guide  

1.  [Research and Development](index.html)

Research and Development : Developer Guide
==========================================

Created by Marius Marcu, last modified by Donald Thompson on Jul 12, 2018

Old Word Doc: [OLD Developer Guide OLD](https://maanaio-my.sharepoint.com/:w:/g/personal/mmarcu_maana_io/ETNlfNIDxs1LkjOnN_0kkGQBRVxo1RsCZ9LdRFwXKTmwsw?e=mctYOb)

  

Document name

Audience

Description

Knowledge Technology Development Guide

Solution Engineers and Data Scientists

details the architecture of the platform, how to build a full solution and/or reusable components

Table of Contents
=================

/*<!\[CDATA\[*/ div.rbtoc1532523907698 {padding: 0px;} div.rbtoc1532523907698 ul {list-style: disc;margin-left: 0px;} div.rbtoc1532523907698 li {margin-left: 0px;padding-left: 0px;} /*\]\]>*/

*   [Table of Contents](#DeveloperGuide-TableofContents)
*   [Introduction](#DeveloperGuide-Introduction)
*   [GraphQL and Microservices](#DeveloperGuide-GraphQLandMicroservices)
    *   [GraphQL Learning Resources](#DeveloperGuide-GraphQLLearningResources)
*   [Knowledge Microservices and Bots](#DeveloperGuide-KnowledgeMicroservicesandBots)
    *   [Development Stages](#DeveloperGuide-DevelopmentStages)
        *   [Stage: Design](#DeveloperGuide-Stage:Design)
        *   [Stage: Local Service (Standalone)](#DeveloperGuide-Stage:LocalService(Standalone))
        *   [Stage: Local Service (Maana)](#DeveloperGuide-Stage:LocalService(Maana))
        *   [Stage: Unmanaged Service](#DeveloperGuide-Stage:UnmanagedService)
        *   [Stage: Managed Service](#DeveloperGuide-Stage:ManagedService)
    *   [Debugging a Knowledge Microservice](#DeveloperGuide-DebuggingaKnowledgeMicroservice)
    *   [The BotAction Protocol](#DeveloperGuide-TheBotActionProtocol)
*   [Knowledge Applications](#DeveloperGuide-KnowledgeApplications)
*   [Development Environments](#DeveloperGuide-DevelopmentEnvironments)
    *   [Visual Studio Code](#DeveloperGuide-VisualStudioCode)
        *   [Command Line](#DeveloperGuide-CommandLine)
        *   [Recommended Plugins](#DeveloperGuide-RecommendedPlugins)
        *   [Settings](#DeveloperGuide-Settings)

Introduction
============

The Maana platform is built from a network of **GraphQL**-based **microservices**. Unlike pure client-server or n-tier architectures, Maana's microservices act as peers in an asynchronous and loosely-coupled arrangement that promotes independent scaling and extensibility. These services provide identity and access controls, graph persistence, search, machine learning, and natural language processing. Ultimately, these services provide reasoning capabilities to Knowledge Applications, which help solve domain-specific problems and support optimal decision-making capable of learning over time.

This guide is intended for Solution Developers and Data Scientists and describes how to develop such services (aka "bots") and applications and operationalize them in a production setting.

GraphQL and Microservices
=========================

At the core of any Maana solution sits a Knowledge Graph, which consists of **concepts** and **properties**, **instances** and **values**, and **relations** and **links**. For example, consider the concept of a _ContainerShip_ with properties _name_, _length_, _position_, etc. A specific instance (entity) has values for each of the properties, such as the Maersk Viking with a length of _400 meters_, ... Such properties can be _scalar_ (e.g., numbers, strings, dates) or might refer to other concepts/instances, e.g., ships _hold Cargo_. In some cases, property values for an instance are simply stored, since they don't change (often). In other cases, they are dynamically computed, such as a ship's _weight_ (which depends on its cargo) or its _current position_ (which requires getting a GPS reading).

**GraphQL** is a data query language created by Facebook and open-sourced in 2015 as an alternative to REST interfaces. Maana uses GraphQL to represent and expose its Knowledge Graph

Unlike a traditional **graph database**, Maana incorporate arbitrary computation (through custom GraphQL resolvers) and distributes the graph into subgraphs managed by different **microservices**, optionally with their own dedicated persistence mechanism.

While this approach allows for flexibility, it places more responsibility on the microservices to provide their own storage. To address this, Maana proposes an explicit split between the data models (i.e., GraphQL type definitions) that a service uses and its operations (i.e., GraphQL resolvers). Maana will generate the appropriate managed service for such models using **KindDB**, Prisma, neo4j, ... The solution developer, then, only provides the logic they care about and let the system take care of all the CRUD/ORM-like operations on the data.

The **Computational Knowledge Graph (CKG)**, itself a microservice, provides automatic persistence, boilerplate queries and mutations, and service orchestration capabilities. Taken together, these service allow the solution developer to focus on **designing GraphQL schemas** and implementing **computational resolvers** only where needed.

GraphQL Learning Resources
--------------------------

*   [How to GraphQL](https://www.howtographql.com/)

Knowledge Microservices and Bots
================================

A **GraphQL service** (endpoint) consists of:

*   **types**
*   **queries**
*   **mutations**
*   **events** (pub/sub)

A **Knowledge Microservice** is a GraphQL service that also includes:

*   authenticated access
*   client/server boilerplate
*   reliable messaging using RabbitMQ
*   lifecycle management (info, register, deregister)
*   Docker containerization and automatic scaling/load balancing

A **Bot** is a Knowledge Microservice that provides specialized queries and mutations that perform **[BotActions](https://confluence.corp.maana.io/display/RD/Bot+Actions)**, which allow the bot to provide asynchronous status updates. This enables user interface components to reflect the latest status of **long-running operations** or **automatically triggered events**, e.g., entity recognition, new concept creation, classification.

All **Kinds** (concepts, types) are associated with a service. This service is said to _provide_ the (entities of) the Kind. Many Kinds are purely extensional (i.e., data) and do not have custom CRUD behavior. Such Kinds are automatically managed by CKG and stored in the KindDB, where it will be indexed for efficient search and querying (including sophisticated entity cooccurrences).

Services also **depend** on **existing** **Kinds** and **queries**, **mutations**, and **events**. The services that provide these Kinds (which may be fully managed by CKG/KindDB) can be **imported** into a new service purely through GraphQL and specified in a **manifest** that is used to **create and register** a new service. This process will result in a **merged schema** on a **service-specific endpoint** that the newly developed service uses for _all_ Maana GraphQL communication (non-pub/sub).

Development Stages
------------------

*   **Design:** focus on GraphQL types (**Kinds**, **Properties**, **Relations**), queries and mutations (often based on **Problem Questions**), and events (consumed and produced)
*   **Local Service (Standalone)**: choose programming language, identify dependencies (libraries, existing services and domain models), core logic to satisfy GraphQL interface contract
*   **Local Service** (**Maana)**: continue local development, connecting to a **remote** Maana deployment (e,g, Azure) to consume and produce GraphQL other services via your **custom endpoint**
*   **Unmanaged Service**: deploy your service via your own means (e.g., Heroku), but make it accessible to Maana to communicate to for call and event dispatching
*   **Managed Service**: Dockerize your service and make it available in a registry that Maana has access to (e.g., Azure)

The following sections will elaborate on each of these stages.

### Stage: Design

At this point, the overall problem to be solved has been analyzed and a **domain model** and set of _decomposed_ **problem questions** has been generated, some discussion of **entity sources** and **data science** has taken place, and it is now time to code one or more **Knowledge Microservices** to provide some new **concepts** (types, Kinds) along with **queries**, **mutations**, and **events** that involve them.

**Focus on the GraphQL**. This is the entire description of and interface to the _world_ for the Knowledge service/bot. Define the **GraphQL SDL** a file, e.g., \`model.gql\`, including custom queries, mutations, and publications/subscriptions. Plan the custom **resolvers**, i.e., what is their **core logic**?

### Stage: Local Service (Standalone)

With at least an initial design complete, the following implementation decisions must be made:

*   Are there existing Kinds and Services that can be reused?
*   Are their existing code libraries or ML models that can be reused?
*   What is the best programming language for this task?
*   Is there reference data or domain data?
*   Are there long-running tasks?
*   What are the scale, performance, and capacity factors?

Once a programming language has been chosen, then an [existing project template](https://github.com/maana-io/Q-ksvc-templates) can be used to scaffold a new Knowledge Microservice/Bot in Scala/JVM, Python, JavaScript, ...

Development of core logic or machine learning solution happens as it normally would, writing, testing, and debugging code or improving model accuracy.

The Knowledge Service is a standard GraphQL endpoint, so it can be run and tested within the development environment and used via [Maana CLI](https://www.npmjs.com/package/graphql-cli-maana), its own exposed [GraphiQL](https://github.com/graphql/graphiql), a standalone [GraphQL Playground](https://github.com/prismagraphql/graphql-playground), etc.

Dependent types and services are **imported** into the service **model** using a modified version of a technique from [graphql-import](https://github.com/prismagraphql/graphql-import):

```graphql
# import File, Url, MimeType from io.maana.core @PASSTHRU

input CrawlOptions {
  url: Url
  ...
}


...


type Mutation {
  crawl(input: CrawlOptions): BotAction
  ...
}
```

### Stage: Local Service (Maana)

In the previous stage, it is likely that various calls/services were stubbed or mocked because they required accessing a Maana cluster. It is now time to interact with a Maana deployment, typically one dedicated to development.

Communication to a Maana system requires the use of authentication. This is configured differently, based on the language/framework being used. Refer to the project template documentation used to scaffold the Knowledge service project.

The service being development must be registered with Maana. This is typically done programmatically using the Maana CLI by specifying a **manifest** that describes the service and its dependencies. For example:

```javascript
{
  id: "io.maana.azure.crawler",
  name: "Maana Azure Storage Crawler Service",
  dockerRegistry: null,
  hostedUrl: null,
}
```

The result of registering a new service with Maana is that CKG will generate a dedicated service endpoint for the new service, e.g., [https://knowledge.acme.com/service/io.maana.azure.crawler:7331.](https://knowledge.acme.com/service/io.maana.azure.crawler:7331.) This enables the standalone service to communicate to Maana, but does not allow Maana to dispatch calls to the standalone service due to network connectivity restrictions. (This will be overcome in the next stages.)

The full GraphQL schema for a service manifest is:

```graphql
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

### Stage: Unmanaged Service

When it is time to have full participation in the Maana processing network, i.e., having its endpoint services consumed by other services, user interfaces, or automatically based on event subscription, then Maana's CKG must be able to communicate to the service. This means that the service must itself be deployed to a host that is accessible from the Maana cluster, e.g., Heroku, Azure, AWS, on-premise. In this configuration, the service owner is responsible for deploying, monitoring, scaling, securing, etc. the service, since Maana only has knowledge of a GraphQL endpoint URL and the schema it provides.

See the [Deploy to Heroku tutorial](https://confluence.corp.maana.io/display/RD/Training).

### Stage: Managed Service

Maana can completely manage a **containerized** Knowledge Microservice/Bot, by specifying a Docker registry that the Maana deployment has access to (e.g., DockerHub, Azure). The following additional information is required when configuring a service to be managed by Maana:

*   scale stuff ??
*   ?? [Andrey Batyuk](https://confluence.corp.maana.io/display/~andrey)

Debugging a Knowledge Microservice
----------------------------------

[Andrey Batyuk](https://confluence.corp.maana.io/display/~andrey)

  

The BotAction Protocol
----------------------

See [technical design note](https://confluence.corp.maana.io/display/RD/Bot+Actions).

Knowledge Applications
======================

Solution developers can develop knoweldge applications on top of the MAANA knwoledge graph.

*   who is the audience for the applications?
    *   SME and are looking for answers to specific questions (i.e. Given a ship, a ship route and an omitted port, what re-route options can be considered to minimize trip duration for the loads onboard?)
*   what type of applications can be developed?
    *   custom web apps, Power BI apps, Tableau, Spotfire? others?
*   what are the steps to build an application?
*   how does a developer publish and manage/share/update/delete an app?

  

Development Environments
========================

Visual Studio Code
------------------

Most people run the ["insiders" edition](https://code.visualstudio.com/insiders/) to have access to latest features.

### Command Line

Add VS Code to your path so you can invoke it from the command line

```bash
export PATH="$PATH:/Applications/Visual Studio Code - Insiders.app/Contents/Resources/app/bin"
```

The 'code' command will now be available in your terminal.

### Recommended Plugins

```bash
code --install-extension PeterJausovec.vscode-docker
code --install-extension christian-kohler.npm-intellisense
code --install-extension christian-kohler.path-intellisense
code --install-extension dbaeumer.vscode-eslint
code --install-extension eamodio.gitlens
code --install-extension eg2.vscode-npm-script
code --install-extension esbenp.prettier-vscode
code --install-extension kumar-harsh.graphql-for-vscode
code --install-extension mikestead.dotenv
code --install-extension ms-azuretools.vscode-azurefunctions
code --install-extension ms-vscode.azure-account
code --install-extension msjsdiag.debugger-for-chrome
code --install-extension robertohuertasm.vscode-icons
```

### Settings

Access user settings from "Code Insiders / Preferences / Settings" and paste the following settings in the "User Settings" tab.

```json
{
    "workbench.iconTheme": "vscode-icons",
    "workbench.colorTheme": "Default High Contrast",
    "editor.fontFamily": "'Roboto Mono Light For Powerline', Menlo, Monaco, 'Courier New', italic",
    "editor.formatOnSave": true,
    "editor.tabSize": 2,
    "editor.insertSpaces": true,
    "eslint.autoFixOnSave": true,
    "files.associations": {
        "*.js": "javascriptreact"
    },
    "explorer.confirmDragAndDrop": false,
    "explorer.confirmDelete": false,
    "window.zoomLevel": 0,
    "gitlens.advanced.messages": {
        "suppressCommitHasNoPreviousCommitWarning": false,
        "suppressCommitNotFoundWarning": false,
        "suppressFileNotUnderSourceControlWarning": false,
        "suppressGitVersionWarning": false,
        "suppressLineUncommittedWarning": false,
        "suppressNoRepositoryWarning": false,
        "suppressUpdateNotice": false,
        "suppressWelcomeNotice": true
    },
    "search.exclude": {
        "**/node_modules": true,
        "**/.git": true
    },
    "git.autofetch": true
}
 ```

Some of these are team style, some are personal preference (e.g., workbench.colorTheme).

Document generated by Confluence on Jul 25, 2018 06:05

[Atlassian](http://www.atlassian.com/)