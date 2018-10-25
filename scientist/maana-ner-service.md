## Maana NER service

This is the maana NER (Named Entity Recognition) graphql microservice

## Environment
The service is built in java, runs on Eclipse jetty server and uses the maven build tool. We currently use the following versions:
````$xslt
Apache Maven 3.5.2 (138edd61fd100ec658bfa2d307c43b76940a5d7d; 2017-10-18T00:58:13-07:00)
Java version: 1.8.0_121, vendor: Oracle Corporation
````
The service might have problem with Java 9, so please try to use the same version as specified for development. 
Unlike java, we do not anticipate any problems with using a different maven version. But, using 3.5 is recommended.

## Building the project via maven
you need maven, if you don't have it install it following steps in https://maven.apache.org/install.html.
If you are in OSX land then you can try installing via Hombrew (http://brewformulas.org/Maven) as follows:
````
brew install maven
````
you can check if maven is installed correctly on your system as follows:
````$xslt
mvn -version
````
After installing Maven Run this command which erases all build (class and target) directories and rebuilds it: 

````
mvn clean install
````
This should pull in and resolve dependencies, compiles and packages the endpont into a WAR file. The WAR file is needed for building the docker image.

## Running The endpoint

Run this command to start the application in jetty server:

````
mvn jetty:run
````
This runs the endpoint in Eclipse Jetty webserver on port 9999.

## Using

you can go to http://localhost:9999, which brings up the graphiql interface.

An example query to run with default model would be this:

```
{
  extract(sources: ["Reaming down from 6000ft to 8000ft to clear stuck pipe. John, please get that article on www.linkedin.com or https://google.com or 192.67.23.222 from file bla123bla.doc and itisme.jpg to me by 5:00PM on Jul 4th 2018 or 4:00 am on 01/09/12 would be ideal, actually. If you have any questions about \"Maana\" or 'Google' office at \"New York\" you can reach my associate at (012)-345-6789 or (230) 241 2422 or +1(345)876-7554 or associative@mail.com or &lt;abracadabra123@maana.io>. Send me $5,987.56 or £4,123.14 or € 100 by PayPal. My SSN is 456-23-0965 My coordinates are: 47.617640, -122.191905 or 47°37'03.5\"N 122°11'30.9\"W"]) {
    fromSpan
    fromOffset
    toKindName
    surfaceForm
  }
}
```
Feel free to change the text in this example.

## Using with docker

### To Build the docker image for your service
**make sure that you have <span style="color:blue">maana-ner-service.war</span> in the target folder.**
If you don't have the war file, you can build it via 'mvn clean install' from this folder
```
docker build -t maana-ner-service .
```

### To run your image
```
docker run --rm -it -p 9999:9999 maana-ner-service
```
## Detected Entities:
Maana-NER-Service detects entities in text data using:
 - Stochastic (CRF-classifier) and
 - Deterministic (Token-Regex) methods. File: **/maana-ner-service/src/main/resources/Regex.rules**

 n  | Entity               | CRF | Regex |
 ---|----------------------|-----|-------|
 01 | DateKind             |  +  |   +   |
 02 | TimeKind             |  +  |   +   |
 03 | Person               |  +  |   -   |
 04 | Location             |  +  |   -   |
 05 | Organization         |  +  |   -   |
 06 | Currency             |  +  |   -   |
 07 | Number               |  +  |   -   |
 08 | Percentage           |  +  |   -   |
 09 | URL                  |  -  |   +   |
 10 | Email                |  -  |   +   |
 11 | PhoneNumber          |  -  |   +   |
 12 | SocialSecurityNumber |  -  |   +   |
 13 | IpAddress            |  -  |   +   |
 14 | WebLink              |  -  |   +   |
 15 | GeoCoordinate        |  -  |   +   |

There is also a capability to use customer’s Token-Regex rules or CRF-classifier from external server if specify URL-path in the parameter modelURL of query:

```
{
  extract( kindId: ID, fieldId: ID, instanceIds: [ID], sources: [String]!, modelURL: String) {
    fromSpan
    fromOffset
    toKindName
    surfaceForm
  }
}
```

## Accuracy Measurement
To measure an accuracy of Maana-NER-Service we used 1700 sentences from BOEM log (oil & gas) dataset.

### Accuracy Test
 - File: **/maana-ner-service/src/test/java/io/maana/stanford/nlp/AccuracyTest.java**

### Statistics:
- Number of Expected Entities
- Number of Observed Entities
- TruePositive = Sum of detected parts (entity may be detected partially) of Expected Entities
- FalseNegative = Sum of missed parts of Expected Entities
- FalsePositive = Sum of detected parts of Unexpected Entities
- Precision = TruePositive / (TruePositive + FalsePositive)
- Recall = TruePositive / (TruePositive + FalseNegative)
- Accuracy = F1 = Precision * Recall / (Precision + Recall)

### Test Results:
Results of Accuracy Test on BOEM dataset with 1700 sentences:
 - File: **/maana-ner-service/src/test/resources/BOEM_labeled.json**

 Entity       | expect # | observ # | True+ | False+ | False- |  Pre  |  Rec  |  F1   |
 -------------|----------|----------|-------|--------|--------|-------|-------|-------|
 Email        |      267 |      267 |   267 |      0 |      0 | 1.000 | 1.000 | 1.000 |
 URL          |        7 |        7 |     7 |      0 |      0 | 1.000 | 1.000 | 1.000 |
 PhoneNumber  |      105 |      104 |   104 |      0 |      1 | 1.000 | 0.990 | 0.995 |
 TimeKind     |      766 |      780 |   754 |     70 |     12 | 0.915 | 0.984 | 0.948 |
 DateKind     |     1044 |     1115 |  1024 |     95 |     20 | 0.915 | 0.981 | 0.947 |
 Person       |     2165 |     2068 |  1963 |     93 |    202 | 0.955 | 0.907 | 0.930 |
 Location     |     1817 |     1296 |  1102 |    221 |    715 | 0.833 | 0.607 | 0.702 |
 Organization |     2191 |      937 |   620 |    534 |   1571 | 0.537 | 0.283 | 0.371 |
 Over All     |     8362 |     9533 |  5842 |   1013 |   2520 | 0.852 | 0.699 | 0.768 |

### Example:

#### Text:
Thanks, Lynard Carter Workover/Completion Engineer U.S. Department of The Interior Bureau of Ocean Energy Management Reg., & Enforcement New Orleans District (504) 734-6746 phone (504) 734-6741 fax Lynard.carter@boemre.gov

#### Expected:
 - Person: **Lynard Carter**
 - Organization: **U.S. Department of The Interior Bureau of Ocean Energy Management Reg., & Enforcement**
 - Location: **New Orleans District**
 - PhoneNumber: **(504) 734-6746**
 - PhoneNumber: **(504) 734-6741**
 - Email: **Lynard.carter@boemre.gov**

#### Observed:
 - Person: **Lynard Carter**
 - Location: **U.S.**
 - Organization: **Interior Bureau of Ocean Energy Management Reg.**
 - Location: **New Orleans**
 - PhoneNumber: **(504) 734-6746**
 - PhoneNumber: **(504) 734-6741**
 - Email: **Lynard.carter@boemre.gov**

 Entity       |  TP   |  FN   |  FP   |  Pre  |  Rec  |  F1   |
 -------------|-------|-------|-------|-------|-------|-------|
 Person       | 1.000 | 0.000 | 0.000 | 1.000 | 1.000 | 1.000 |
 Email        | 1.000 | 0.000 | 0.000 | 1.000 | 1.000 | 1.000 |
 PhoneNumber  | 2.000 | 0.000 | 0.000 | 1.000 | 1.000 | 1.000 |
 Organization | 0.553 | 0.447 | 0.000 | 1.000 | 0.553 | 0.356 |
 Location     | 0.550 | 0.450 | 1.000 | 0.350 | 0.550 | 0.214 |

#### Explanation:

#### Organization:
 - Expected: **U.S. Department of The Interior Bureau of Ocean Energy Management Reg. , & Enforcement**, offset: 51; span: 85
 - Observed: **Interior Bureau of Ocean Energy Management Reg.**, offset: 74; span: 47

TP = 47/85 = 0.553, FN = 1 - TP = 0.447

#### Location:
 - Expected: **New Orleans District**, offset: 137; span: 20
 - Observed: **New Orleans**, offset: 137; span: 11

TP = 11/20 = 0.55, FN = 1 - TP = 0.45

#### Location:
 - Observed but not expected: **U.S.**, offset: 51; span: 4

TP = 0, FN = 0, FP = 1