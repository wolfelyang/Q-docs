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

**Optionally**, to create a configuration from scratch, create a CKG project and GraphQL endpoint, as in:

```bash
gql init
? Enter project name (Enter to skip): ckg
? Local schema file path: ckg.graphql
? Endpoint URL (Enter to skip): https://stable.knowledge.maana.io:8443/graphql
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
          "default": "https://stable.knowledge.maana.io:8443/graphql"
        }
      }
    }
  }
}

? Is this ok? Yes
```
![](/assets/animations/cli-gif4.gif)

## Authentication with Maana

Maana endpoints require a valid (authenticated) user in order to prevent unauthorized access. You must first obtain a token and the update the configuration to use the token by updating the endpoint configuration.

### <a name="v3.0.5"></a>Maana Q v3.0.5

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



## Maana Q v3.1.0 and later

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

#### Additional Notes

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
gql maddsvc "Drillng Problems" -s model.gql -p ckg
Using endpoint default: {"url":"https://qtraining01.knowledge.maana.io:8443/graphql"}
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

The included `.graphqlconfig` already contains a project, `dp`, that specified the schema file and endpoint for this service. However, the service ID needs to be updated to match the output from adding the service (above):

```
{
  "projects": {
    "ckg": {
      "schemaPath": "ckg.graphql",
      "extensions": {
        "endpoints": {
          "default": {
            "url": "https://latest.knowledge.maana.io:8443/graphql",
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
            "url": "https://latest.knowledge.maana.io:8443/service/50d759d5-983d-4ea2-9773-20077c9b823e/graphql",
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
? Endpoint URL (Enter to skip): https://qtraining01.knowledge.maana.io:8443/service/1788c00e-3a29-4843-aa56-44ba374cf682/graphql
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

![](/assets/animations/cli-gif15.gif)
