# v3.1.2


## New Features and Capabilities

| Feature | Description |
| ----------- | -----------|
| Improved system monitoring and logging | Grafana dashboards have been updated to include more detailed views of key system metrics (see URL http://<host name>:4000/). Platform log messages collected via Graylog have been improved to provide more precise information (see URL https://<host name>:8443/graylog/ui). |
| Split Natural language Processing Service | The Natural Language Processing Service has been divided into the two independent Natural Language Processing Service and the Fact Extraction Service. This change brings our Cognitive Services more into line with microservice architecture best practices, and provides a foundation for better scalability and extensibility. The Fact Extraction Service holds all mutation related to fact extraction, whereas the Natural Language Processing Service exposes queries and mutations related to named entity tagging.|
| Improved error handling| Backend system (kindDB) will no longer cache errors. This will prevent situations in which cached errors for previously failed queries caused the system to throw the cached error for new submissions of those queries, instead of retrying.|
|   New text extraction function **(A)** |   The Maana "Text Extractor" Knowledge Service is able to extract textual information from various file formats (PDF, DOC, DOCX, PPT, PPTX, XLS, XLSX) and to convert files to XHTML. |


**(A) Extracted text can be accessed using the following Query (assuming that the service has been triggered via user dropping document on the UI canvas):**



```bash
#this query will return all extracted text from the specified document
#run this query from the Maana Text Extractor service. If not in inventory, search and add to inventory

Query extractText{
extractText(
file: {
  id: "7a47d30a-c102-47cb-bba6-bdd99723d98d" #your instance id, obtained from the "document" Kind
  name: "01_Maana_Introduction.pdf"
  url: "https://latest.knowledge.maana.io:8443/downloads/e97737bc-523c-43b4-856b-7648d55bce05/01_Maana_Introduction.pdf" #obtained from the relevant instance of "file" kind
  status: 200
    }
  )
}
```


```bash
#this query will return all extracted XML from the specified document
#run this query from the Maana Text Extractor service. If not in inventory, search and add to inventory

Query extractXHTML{
  extractText(
	file: {
		id: "7a47d30a-c102-47cb-bba6-bdd99723d98d" #your instance id, obtained from the "document" Kind
		name: "01_Maana_Introduction.pdf"
		url: "https://latest.knowledge.maana.io:8443/downloads/e97737bc-523c-43b4-856b-7648d55bce05/01_Maana_Introduction.pdf" #obtained from the relevant instance of "file" kind
		status: 200
			}
		)

	}

```


## Fixed Issues




|Fixes & improvements|
|------|
|ENG-14448 Fixed issue with platform not creating services from CLI related to error message 'Cannot read property 'add' of undefined'.|
|ENG-14379 Fixed issue with kind names capitalization and querying.|
|ENG-14359 Fixed issue with search crashing platform storage service.|
|ENG-14341 Fixed issue with search not returning all results.|
|Miscellaneous cosmetic fixes for the UI.|
|Updated and added clarification to Q Tutorial documentation on [GitHub](https://github.com/maana-io/Q-tutorials/tree/master/cli).|



## Known Issues

|Issue Description|
|------|
|   It is a known issue that not all logs will show in in Graylog when a cluster is first deployed, as services may start before the Graylog server (stack) is completely up and running.|
|   Currently the ingestion process using CLI mutations does not perform optimally unless the batch size is set to more than 3000 per chunk, which can be set using the argument -b.|
|  When large amounts of text data is added through the UI, the platform will become unresponsive. This is the result of the entity extractor bot performing a large number of writes to record the links to entities that it has found in the text. To prevent this, it is necessary to disable the entity extractor bot either by removing its instances following the code block below **(B)** or by commenting it out in Docker Compose file.|
|  Users cannot currently truly delete instances of kinds from the graph through the UI, though it can be achieved using graphQL mutations.  |
|  Certain events are not automatically recognized by the UI, and require the user to refresh to see the resultant changes. Uploading a CSV or Image requires a refresh before the hasKind or hasImage Links will be shown within the golden instance node that is created.|
|   It is not possible to edit the type of a kind's field between certain classes of scalar if that kind is populated by instances. For example, if you have uploaded data to a kind that contains a field of type STRING, we do not currently support the action of changing that field to type FLOAT. |
|  If you attempt to upload a large number of records containing bad data (such as badly formatted dates), there is a high-likelihood of system failure. Multiple errors will be raised by each bad record in the batch, and all of these will be thrown back to the loading client at once.|
|  If you attempt to upload a large number of records containing bad data (such as badly formatted dates), there is a high-likelihood of system failure. Multiple errors will be raised by each bad record in the batch, and all of these will be thrown back to the loading client at once.|
|  For security reasons, we are currently not allowing services that are not deployed along with the Q platform to participate in the pub-sub network.|
|  The version of the CLI needs to be at least v3.1.0 to work correctly with v3.1.0 version of the Q platform. The CLI versions can be updated through the Maana Q github repository: [Maana Q github repository](https://github.com/maana-io/Q-cli/blob/master/README.md#to-build-and-install) .|
|  It is not possible to link an ID field to a string type.|
|  Non-null fields can report missing value when using mutation of valid field. Auto-generated input types for mutations don't honor NONULL modifier|


**(B) Disable the entity extractor bot either by removing its instances:**
```bash
docker service scale <stack name>_maana-entity-extractor=0
```
# v3-0-5

## New Features and Capabilities


| Feature | Description |
| ----------- | -----------|
| HTTPS Encryption    |   Maana now supports HTTPS encryption.       |
| Authentication    |   Maana Q now requires authentication for all clients that attempt to connect to the Q servers.|
| Azure Storage Crawler Knowledge Service |   We developed a service that allows users to crawl Azure File and Blob directories, to discover and build a file catalog of the contents.      |
| Text Extractor   |   We have added a service that extracts all textual information from files of type PDF, DOC, DOCX, PPT, PPTX, XLS, and XLSX, and makes it available in the kind associated with the file in Maana.     |
| Add Files by URL   |   MIt is now possible to add a file to Maana through the UI by providing that file's URL, accessed using the "upload" icon in the Explorer tab of your workspace.      |
| New Query Graph Filters   |   When using the Query Graph to filter a kind's instances based on field values, users are now able to use the logical arguments "is not equal to" and "is one of" in addition to the original "is equal to".    |
| Improved developer experience    |   Support giving services names, rather than relying on IDs, and support replacing services in-place rather than create new ones.      |


## Fixed Issues

| Feature |
| -----------|
|Users now able to upload data sets with more than ~20k records using the CLI without receiving the "queryResult.forEach is not a function" error |
| Improvements in the in-node editing experience through the UI |
| Loading data through the CLI no longer throws a FieldMismatch error when the CSV being loaded has empty rows at the end |
| UI stability improvements |
| Improvements to the CKG interactions through CLI |
| Improvements to the Search feature in the UI |
| Proper handling of DateTime/Date types|


## Known Issues


| Issue Description |
| -----------|
| Upon deployment, it is possible for the text extractor service to start in a bad state and require a restart of that service. To restart the service, you can use the commands: **(A)**  |
| Currently the ingestion process does not perform well unless the batch size (such as when using the CLI add mutations) is set to more than 3000 per chunk.|
| When large amounts of text data is added through the UI, the platform will become unresponsive. This is the result of the entity extractor bot performing a large number of writes to record the links to entities that it has found in the text. To prevent this, it is necessary to disable the entity extractor bot. |
| Users can not currently truly delete instances of kinds from the graph. This is because we have not yet implemented a way of allowing users to delete certain kinds without allowing them to also delete system kinds. |
| Certain events are not automatically recognized by the UI, and require the user to refresh to see the resultant changes. Uploading a CSV or Image requires a refresh before the HasKind or HasImage Links will be shown within the golden instance node that is created. |
| It is not possible to edit the type of a kind's field between certain classes of scalar, if that kind is populated by instances. For example, if you have uploaded data to a kind that contains a field of type STRING, we do not currently support the action of changing that field to type FLOAT. |
| If you attempt to upload a large number of records containing bad data (such as badly formatted dates), there is a high-likelihood of system failure. Multiple errors will be raised by each bad record in the batch, and all of these will be thrown back to the loading client at once. |
| As currently architected, services that are not deployed within the cluster containing the Maana platform are unable to access RabbitMQ, and as such are unable to participate in the pub-sub network. Planning a fix for v3.1.0. |
| The version of the CLI needs to be at least v1.0.36 to work correctly with v3.0.5.|
| All instances created in a single mutation must be created with a consistent structure (i.e. set of fields) or data will be lost. Fields without values must have an empty placeholder or empty value. |


**(A) Command to Restart Service**
```bash
docker service update {stack\_name}\_maana-text-extractor --force.
```
