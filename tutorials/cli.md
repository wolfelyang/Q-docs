# CLI Tutorials

*Collection of 'Hello World' training to onboard users onto the Maana CLI*
____

# Drilling Problems

In this tutorial, we will walk step-by-step for creating a managed graph from a GraphQL definition, loading it with data, adding custom services, and building a knowledge application.

## Note - GIT is a required prerequisite for this training.

To download GIT please use the following instructions:
https://git-scm.com/book/en/v2/Getting-Started-Installing-Git


## Clone Repo

  1. Nagivate to https://github.com/maana-io/Q-tutorials
  2. Open up terminal or console and navigate to your desired folder
  3. Run the following commands :

```bash

  git clone https://github.com/maana-io/Q-tutorials.git

```

![](/assets/animations/cli-gif1.gif)


## Installation

We use the GraphQL command line interface (CLI) with the Maana plugin:

```bash
  npm i -g graphql-cli graphql-cli-maana
```

![](/assets/animations/cli-gif2.gif)

![](/assets/animations/cli-gif3.gif)

## Setup

**We have included a `.graphqlconfig` file preconfigured for this tutorial. Minimally, ensure the endpoint is correct for the Maana endpoint you are using. If you have any questions about the correct Maana endpoint please contact your Maana Solutions Architect Team, or your Internal Maana Administrator**

Edit the preconfigured `.graphqlconfig`, copy the code below into your `.graphqlconfig` file, and be sure to *update your endpoint URL*

```
{
  "projects": {
    "ckg": {
      "schemaPath": "ckg.graphql",
      "extensions": {
        "endpoints": {
          "default": "https://<INSERT ENDPOINT URL>:8443/graphql"
        }
      }
    }
  }
}
```
____
**Optionally**, to create a configuration from scratch, create a CKG project and GraphQL endpoint, as in:

```bash
gql init
? Enter project name (Enter to skip): ckg
? Local schema file path: ckg.graphql
? Endpoint URL (Enter to skip): https://<INSERT ENDPOINT URL>:8443/graphql
? Name of this endpoint, for e.g. default, dev, prod: (default)
? Subscription URL (Enter to skip):
? Do you want to add other endpoints? No
? What format do you want to save your config in? JSON

About to write to /home/me/maana/training/.graphqlconfig:

{
  "projects": {
    "ckg": {
      "schemaPath": "ckg.graphql",
      "extensions": {
        "endpoints": {
          "default": "https://<INSERT ENDPOINT URL>8443/graphql"
        }
      }
    }
  }
}

? Is this ok? Yes
```
![](/assets/animations/cli-gif4.gif)
____

## Authentication with Maana

Maana endpoints require a valid (authenticated) user in order to prevent unauthorized access. You must first obtain a token and the update the configuration to use the token by updating the endpoint configuration.


### Maana Q v3.1.0 and later

After creating a new `.graphqlconfig` file connecting to a Maana API endpoint:
* Login to the Maana Knowledge Portal
* Click on your user icon and select your profile
* At the bottom of the profile page click the 'Get CLI Authentication Token' button
* Go through the login process (again)
* Copy the generated auth token that shows up below the button
* In the terminal run `gql msignin` and when asked paste the Authentication Token into the prompt

```sh
  $ gql msignin
```

![](/assets/animations/cli-gif8.gif)

* Run `gql menv --shell <your shell>` in the current terminal window to run authenticated GraphQL calls.
  - Example: Run `gql menv --shell bash` if you are using bash
  ```sh
    $ gql menv --shell bash
  ```
  - You will see output similar to:
    ```sh
    export MAANA_AUTH_TOKEN=<token here>
    # Run this command to configure your shell
    # eval $(gql menv --shell bash)
    ```
  - Now run `eval $(gql menv --shell bash)` like it asks you at the bottom of the output
  ```sh
    eval $(gql menv --shell bash)
  ```

* Run `gql ping` to test out that the authentication works (you will get an error if it did not)
  ```sh
    gql ping
  ```

![](/assets/animations/cli-gif9.gif)

### Maana Q v3.0.5

* Login to the Maana Knowledge Portal
* Click on your user icon and select your profile
* At the bottom of the profile page click the 'Get CLI Authentication Token' button
* Login Again (For Enhanced Security)
* Copy the generated auth token that shows up below the button

![](/assets/animations/cli-gif5.gif)


* In the terminal add an environment variable for the auth token

```sh
# *nix based systems
export AUTH_TOKEN_ENV=<paste auth token here>
```

```bat
rem windows command line
set AUTH_TOKEN_ENV=<paste auth token here>
```

```ps1
# windows power shell
$Env:AUTH_TOKEN_ENV = "<paste auth token here>"
```


![](/assets/animations/cli-gif7.gif)


### Additional Notes

* When you add another project to your `.graphqlconfig` file you can run `gql maddheaders --project <Project Name>` to add the headers to the new project.
* When you want to run the CLI against the Maana API in a different terminal window you will need to run `gql env` again.
* If your authentication token expires you can run `gql mrefreshauth` to refresh the authentication token, when the Maana API is configured to allow the refreshing of authentication tokens.



## Create the Service

The domain model for the tutorial has already been created: `model.gql`. This GraphQL SDL file contains only the types we care about: Well, Location, DrillingReport, DrillingProblem, ...

We would like to have Maana completely manage these types, creating the boilerplate queries, mutations, and subscriptions covering the basic CRUD (Create, Read, Update, Delete) operations.

We can use the GraphQL CLI with the Maana plugin command: `maddsvc` ("add service"):

```bash
  Navigate to the root directory drill_prob path of the Maana-Q-Training:
  $ cd drill_prob
  $ pwd
    /Q-tutorials/drill_prob
```

```bash
gql maddsvc "Drilling Problems" -s model.gql -p ckg
Using endpoint default: {"url":"https://<INSERT ENDPOINT URL>:8443/graphql"}
Read file: model.gql size: 483
Sending query:

      mutation addServiceSource($input: AddServiceSourceInput!) {
        addServiceSource(input: $input)
      }

✔ Call succeeded:
{"addServiceSource":"50d759d5-983d-4ea2-9773-20077c9b823e"}
```
![](/assets/animations/cli-gif10.gif)

## Update the Config

The included `.graphqlconfig` file (also outlined below) already contains a project, `dp`, that specified the schema file and endpoint for this service. However, the service ID needs to be updated to match the output from adding the service (above) to the code block below:

```
{
  "projects": {
    "ckg": {
      "schemaPath": "ckg.graphql",
      "extensions": {
        "endpoints": {
          "default": {
            "url": "https://<INSERT ENDPOINT URL>:8443/graphql",
            "headers": {
              "Authorization": "Bearer ${env:AUTH_TOKEN_ENV}"
            }
          }
        }
      }
    },
    "dp": {
      "schemaPath": "dp.graphql",
      "extensions": {
        "endpoints": {
          "default": {
            "url": "https://<INSERT ENDPOINT URL>:8443/service/<INSERT YOUR SERVICE ID FROM ABOVE HERE>/graphql",
            "headers": {
              "Authorization": "Bearer ${env:AUTH_TOKEN_ENV}"
            }
          }
        }
      }
    }
  }
}
```

**Optionally**, *to create this project from scratch:*

```bash
gql add-project
? Enter project name for new project: dp
? Local schema file path: dp.graphql
? Endpoint URL (Enter to skip): https://<INSERT ENDPOINT URL>:8443/service/<INSERT YOUR SERVICE ID FROM ABOVE HERE>/graphql
? Name of this endpoint, for e.g. default, dev, prod: (default)
? Subscription URL (Enter to skip):
? Do you want to add other endpoints? No

Adding the following endpoints to your config:  dp
? Is this ok? Yes
```

Again, we need to add the authorization header.
* In Maana Q 3.1.0 or later just run the `gql maddheaders` command
  ```bash
    $ gql maddheaders
  ```

![](/assets/animations/cli-gif11.gif)

* In Maana Q 3.5.0 will require adding it manually, check [here](#v3.0.5) for instructions.

## Introspecting the Service

Only a few types were specified for the **domain model**. Maana adds a set of boilerplate types and operations as part of a fully-managed service. The **schema** for this service can be retrieved by:

```bash
gql get-schema -p dp
```

  ![](/assets/animations/cli-gif13.gif)

## Loading Instance Data

Now that the model has been turned into a service, we can upload instance data to populate ("hydrate") the graph:

```bash
gql mload data/DrillingProblem.csv -p dp
++++++
gql mload data/DrillingReport.csv -p dp
gql mload data/Well.csv -p dp
```

![](/assets/animations/cli-gif14.gif)


For convenience, these steps have been added to a script file: `loadData.sh`

` sh loadData.sh `

![](/assets/animations/cli-gif15.gif)



# Maana Command Line Interface (CLI) Domain Modeling & Querying

Here is a step-by-step example of an end-to-end flow of creating a domain model, hydrating it with instances, and querying it, all programmatically, using the standard [graphql-cli ](https://github.com/graphql-cli/graphql-cli)utility and the custom Maana plugin.

Sample code can be found here: [https://github.com/maana-io/Q-tutorials/tree/master/cli](https://github.com/maana-io/Q-tutorials/tree/master/cli).

It is published as an [NPM package](https://www.npmjs.com/package/graphql-cli-maana).

It is best to make a copy of this repo (delete the .git folder to detach it from GitHub). Then you can make all the changes you want, check it into a different repo, etc.

## Installation

```sh
npm i -g graphql-cli graphql-cli-maana
```

## Setup

The CLI uses a [standard configuration format](https://github.com/graphcool/graphql-config/blob/master/specification.md), .graphqlconfig. The purpose is to provide configurations for the CLI tool.

The tutorial repo you cloned above includes a sample .graphqlconfig. It might be easier to edit it, but, if you wish, you can create your own by (deleting it and) following the instructions below.

The config consists of:

* **projects** : these are equivalent to Maana "service name" and tell the CLI where to find the schema for the endpoint
* **endpoints** : these are the "service endpoint URLs"

For consistency and simplicity, we recommend you use the same name for the **Maana service** , the **project** , and the **endpoint** (e.g., "ckg", "basic", "projectX").

To create a configuration from scratch, create a CKG project and GraphQL endpoint, as in:

```bash
gql init
? Enter project name (Enter to skip): ckg
? Local schema file path: ckg.graphql
? Endpoint URL (Enter to skip): https://<maana host>:8443/graphql
? Name of this endpoint, for e.g. default, dev, prod: (default)
? Subscription URL (Enter to skip):
? Do you want to add other endpoints? No
? What format do you want to save your config in? JSON

About to write to /home/me/maana/training/.graphqlconfig:

{
  "projects": {
    "ckg": {
      "schemaPath": "ckg.graphql",
      "extensions": {
        "endpoints": {
          "default": "https://<maana host>:8443/graphql"
        }
      }
    }
  }
}

? Is this ok? Yes
```

## Authentication with Maana

Maana endpoints require a valid (authenticated) user in order to prevent unauthorized access.

### <a name="v3.1.0"></a>Maana Q v3.1.0 and later

After creating a new `.graphqlconfig` file connecting to a Maana API endpoint:
* Login to the Maana Knowledge Portal
* Click on your user icon and select your profile
* At the bottom of the profile page click the 'Get CLI Authentication Token' button
* Go through the login process (again)
* Copy the generated auth token that shows up below the button
* In the terminal run `gql msignin` and when asked paste the Authentication Token into the prompt
* Run `gql menv --shell <your shell>` in the current terminal window to run authenticated GraphQL calls.
  - Example: Run `gql menv --shell bash` if you are using bash
  - You will see output similar to:
    ```
    export MAANA_AUTH_TOKEN=<token here>
    # Run this command to configure your shell
    # eval $(gql menv --shell bash)
    ```
  - Now run `eval $(gql menv --shell bash)` like it asks you at the bottom of the output
* Run `gql ping` to test out that the authentication works (you will get an error if it did not)

#### Additional Notes

* When you add another project to your `.graphqlconfig` file you can run `gql maddheaders --project <Project Name>` to add the headers to the new project.
* When you want to run the CLI against the Maana API in a different terminal window you will need to run `gql env` again.
* If your authentication token expires you can run `gql mrefreshauth` to refresh the authentication token, when the Maana API is configured to allow the refreshing of authentication tokens.

### <a name="v3.0.5"></a>Maana Q v3.0.5

After creating a new project connecting to a Maana endpoint, you will need to setup the project to add an authentication header to the requests.

* Login to the Maana Knowledge Portal
* Click on your user icon and select your profile
* At the bottom of the profile page click the 'Get CLI Authentication Token' button
* Go through the login process (again)
* Copy the generated auth token that shows up below the button
* In the terminal add an environment variable for the auth token

```sh
# *nix based systems
export MAANA_AUTH_TOKEN=<paste auth token here>
```

```bat
rem Windows command line
set MAANA_AUTH_TOKEN=<paste auth token here>
```

```ps1
# Windows power shell
$Env:MAANA_AUTH_TOKEN = "<paste auth token here>"
```

Add the authorization header to the Maana endpoint:

```diff
     "ckg": {
      "schemaPath": "ckg.graphql",
       "extensions": {
         "endpoints": {
-           "default": "https://<maana host>:8443/graphql"
+           "default": {
+             "url": "https://<maana host>:8443/graphql",
+             "headers": {
+               "Authorization": "Bearer ${env:MAANA_AUTH_TOKEN}"
+             }
+           }
         }
       }
     }
```

## Create the Model

Let's first define a simple schema to use, e.g., model.gql:

```graphql
type Person {
  id: ID!
  name: String!
  dob: String
  employer: Employer
}

type Employer {
  id: ID!
  name: String!
  ceo: Person
}
```

## Create the Service

Now that we've defined our model, we would like Maana to manage it for us (i.e., create a graph and all of the boilerplate operations, such as add, updating, and deleting instances, querying them, generating events, etc.).

Execute the **maddsvc** ("add service") command, which takes the **service name** and the **GraphQL model** definition (i.e., your types, queries, mutations, and subscriptions):

```bash
gql maddsvc Basic -s basic/model.gql -p ckg
Using endpoint default: {"url":"https://<maana host>:8443/graphql"}
Read file: basic/model.gql size: 136
Sending query:
  mutation addServiceSource($input: AddServiceSourceInput!) {
    addServiceSource(input: $input)
  }
  ...
✔ Call succeeded:
{"addServiceSource":"1788c00e-3a29-4843-aa56-44ba374cf682"}
```

Take note of the generated service id, since we&#39;ll add it as a new GraphQL **endpoint** to your CLI configuration.

## Update the Config

Add another project to you graphql config using the following template to build your service url. Make sure that your url ends in `/graphql`.

Template:
```
https://<maana host>:8443/service/<service id>/graphql
```

Command:
```bash
gql add-project
? Enter project name for new project: basic
? Local schema file path: basic/schema.graphql
? Endpoint URL (Enter to skip): <service url>
? Name of this endpoint, for e.g. default, dev, prod: (default)
? Subscription URL (Enter to skip):
? Do you want to add other endpoints? No

Adding the following endpoints to your config:  basic
? Is this ok? Yes
```

Again, we need to add the authorization header.
* In Maana Q 3.1.0 or later just run the `gql maddheaders` command
* In Maana Q 3.5.0 will require adding it manually, check [here](#v3.0.5) for instructions.

## Introspecting the Service

Only a few types were specified for the **domain model**. Maana adds a set of boilerplate types and operations as part of a fully-managed service. The **schema** for this service can be retrieved by:

```bash
gql get-schema -p basic
```

## Creating Instance Data

Create instances from common data formats, such as CSV and JSON that conform to the model. The /basic examples of person and employer instance data are given below.

### person.csv

```baash
"id","name","dob","employer"
"P00","Han Solo","1942-07-13","E00"
"P01","George Lucas","1944-05-14","E00"
```

### employer.csv

```baash
"id","name","ceo"
"E00","Lucasfilm Ltd.","P01"
```

### person.json

```json
[
  {
    "id": "P00",
    "name": "Han Solo",
    "dob": "1942-07-13",
    "employer": "E00"
  },
  {
    "id": "P01",
    "name": "George Lucas",
    "dob": "1944-05-14",
    "employer": "E00"
  }
]
```

### employer.json

```json
[
  {
    "id": "E00",
    "name": "Lucasfilm Ltd.",
    "ceo": "P01"
  }
]
```

## Loading Instance Data

The above CSV and JSON data can be loaded by using the &#39;load&#39; GraphQL CLI command, passing the mutation to call, the data file, field mappings (if any). delimeters, etc.

```bash
gql mload basic/person.json -p basic
gql mload basic/employer.json -p basic
```

## Using Default Queries

The boilerplate for persisted models includes add/update/delete mutations as well as get by id and get batch by ids queries that can be used from GraphiQL, the CLI, or from any GraphQL client. For the above &#39;basic&#39; example, we define the below queries.

This is exactly the same format as you would use in GraphiQL ---- try cut-and-pasting the below into a GraphiQL session against your service.

### basicOps.gql

```graphql
fragment personDetail on Person {
  name
  dob
  employer {
    ...employerDetail
  }
}

fragment employerDetail on Employer {
  name
  ceo {
    name
  }
}

query person($id: ID!) {
  person(id: $id) {
    ...personDetail
  }
}

query allPersons {
  allPersons {
    ...personDetail
  }
}

query employer($id: ID!) {
  employer(id: $id) {
    ...employerDetail
  }
}

query allEmployers {
  allEmployers {
    ...employerDetail
  }
}
```

These queries can be invoked from the command line, such as:

```bash
gql query basic/basicOps.gql -p basic -o allEmployers
gql query basic/basicOps.gql -p basic -o person --variables "{\"id\":\"P01\"}"
```

## Issuing a Kind Query

TODO
