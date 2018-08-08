# UI Tutorials

*Collection of 'Hello World' training to onboard users onto the Maana UI*

____

# Drilling Problems

In this section, we assemble the concepts identified in the Problem Decomposition exercise into a domain model.  The domain model, instantiated in Maana, is a representation of our solution ontology.  We use this exercise as a vehicle to introduce many of the basic actions a user can take in the Knowledge Portal - the Maana platform's user interface.  By the end of this section, a user should have a fully built solution domain model in thier workspace, and understand how to interact with it using the GraphiQL interface.

## Create Workspace:

1. Navigate to the Knowledge Portal URL
2. Log in using Google credentials
3. Navigate to Workspaces
  * Create a new workspace
  * Name the workspace
  * Save

![](/assets/animations/gif1.gif)

## Add Kinds to Workspace:

4. Add two kinds to the workspace, named Location and Well and save
  * Use the context panel (to the right of the screen) to edit the schema of Location to be:
    * lat: Float
    * long: Float
  * Use the context panel (to the right of the screen) to edit the schema of Well to be:
     * name: String
    * location: Location


Location

![](/assets/animations/gif2-1.gif)

Well

![](/assets/animations/gif2-2.gif)

## Hydrate Model With Data:

5. Open the GraphiQL interface by selecting the workspace from the Explorer panel (to your left)
    * ([Learn More About GraphQL Syntax Here](https://graphql.org/learn/))
6. Add an instance of Location using the mutation:


```bash
# AddLocation is the mutation for adding an instance of Location to Maana
# the "Input" argument to the location is a JSON object with arguments for
# the fields you are trying to populate for each instance.
# By specifying the id, we can query this more easily later.  Not specifying
# the id would mean Maana auto-generates one for you.


mutation {
    addLocation(
        input: {
            id: "Location001",
            lat: 1.0,
            long: 2.0
        }
    )
}
```
![](/assets/animations/gif3.gif)



7. Add an instance of Well using the mutation:

```bash
# AddWell is the mutation for adding an instance of Location to Maana
# the "Input" argument to the location is a JSON object with arguments for
# the fields you are trying to populate for each instance.
# By specifying the location as the ID of the Location we added in the
# previous step, we indicate that the location of this well has those coordinates.

mutation {
    addWell(
        input: {
            id: "Well001",
            name: "Test Well",
            location: "Location001"
        }
    )
}
```

![](/assets/animations/gif4.gif)

## Query the model to retrieve data

8.  Demonstrate that this data was successfully loaded into the graph by submitting the following Query

```bash

# The query Well (meant to return a single instance of this kind) takes
# the desired Well's ID as the argument.


query {
    well(id: "Well001"){
        name
        location {
            id
            lat
            long
            177000010301
        }
    }
}
```

![](/assets/animations/gif5.gif)




### Common Errors
Error

![](/assets/animations/gif5-error.gif)


Solution

![](/assets/animations/gif5-error-solution.gif)

## Add additional workspaces to expand the data model

9. Use these skills to add the rest of the concepts discovered in the Problem Decomposition phase to your workspace, completing your Domain Model. Following the steps below:
  * Add four kinds to the workspace, named NearbyWell, DrillingReport, ExpectedDrillingProblem, and DrillingProblem
    * Use the context panel (to the right of the screen) to edit the schema of NearbyWell to be:
      * well: Well
      * distance: Float
    * Use the context panel (to the right of the screen) to edit the schema of DrillingProblem to be:
      * problemType: String
    * Use the context panel (to the right of the screen) to edit the schema of ExpectedDrillingProblem to be:
      * probability: Float
      * drillingProblem: DrillingProblem
    * Use the context panel (to the right of the screen) to edit the schema of DrillingReport to be:
      * well: Well
      * comment: String
      * date: Date
      * drillingProblem : DrillingProblem

![](/assets/animations/gif6.gif)
