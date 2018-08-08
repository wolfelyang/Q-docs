Features
========

The Maana "Text Extractor" Knowledge Service is able to extract textual information from various file formats (see below) and to convert files to XHTML.

Assumptions
===========

1.  Files are not locked or encrypted.
2.  No embedded objects, such as macros, shall be extracted.

Requirements
============

Operational
-----------

*   Subscribes to fileAdded events to automatically process supported k:File instances to:
    *   create an instance of Kind Document
    *   with extracted text
    *   link k:File instance via HasKind relation to k:Document
    *   (this is essentially the mutation below)
*   Queries:  
    *   given a k:File, what is extracted text?
*   Mutations:  
    *   given a k:File, what is extracted and linked k:Document?

File Types to Support
---------------------

*   PDF
*   DOC
*   DOCX
*   PPT
*   PPTX
*   XLS
*   XLSX

See [official Tika documentation](http://tika.apache.org/1.18/formats.html) for details on the aforementioned types.

Schema
======

##### [Most Up To Date Schema File](/DrillingProblems/textextractor.graphql)


Supported File Types
====================

| Type/Extension        | MIME Type |
| :--- | :---|
| PDF/pdf     | application/pdf  |
| Older Word Document/doc   | application/msword  |
|   Newer Word Document/docx   |  application/vnd.openxmlformats-officedocument.wordprocessingml.document |
|   Older PowerPoint/ppt   | application/[vnd.ms](http://vnd.ms)-powerpoint  |
|   Newer PowerPoint/pptx  | application/vnd.openxmlformats-officedocument.presentationml.presentation  |
|   Older Excel/xls   | application/[vnd.ms](http://vnd.ms)-excel  |
|   Newer Excel/xlsx   |  application/vnd.openxmlformats-officedocument.spreadsheetml.sheet |


Example(s)
==========

### HTTP Method: POST

Content-Type: application/json

Input | Output
:--- | :---
Input 1 | Output 1
Input 2 | Output 2
Input 3 | Output 3

#### Input 1
```ruby
    # Input 1
    mutation {

      createDoc(

        file: {

          id:"SOME\_FILE\_ID",

          name: "SOME\_FILE\_NAME",

          url: "http://SOME\_BASE\_URL/SOME_FILE.xls",

          status: 200

        }

      ){id name text}

    }
```
#### Output 1
```ruby
# Output 1
{  
  "data": {  
    "createDoc": {  
      "id": "92054dd5-6939-45eb-bac4-a6f95eb70f9c",  
      "name": "SOME\_FILE\_NAME",  
      "text": "some text found in the file",

      "xhtml": "file content as XHTML"

    }  
  }  
}
```

#### Input 2

```ruby
# Input 2
query {  

  extractText(    

    file: {

      id:"SOME\_FILE\_ID",

      name: "SOME\_FILE\_NAME",

      url: "http://SOME\_BASE\_URL/SOME_FILE.xls",


      status: 200  

    }  

  )
```

#### Output 2
```ruby
# Output 2
}

{  
  "data": {  
    "extractText": "some text found in the file"  
  }  
}

```
#### Input 3
```ruby
# Input 3
query {  

  extractXHTML(    

    file: {

      id:"SOME\_FILE\_ID",

      name: "SOME\_FILE\_NAME",

      url: "http://SOME\_BASE\_URL/SOME_FILE.xls",


      status: 200  

    }  

  )

}
```
#### Output 3
```ruby
# Output 3
{  
  "data": {  
    "extractXHTML": "file content as XHTML"  
  }  
}
```


Development
-----------

> Language: Scala

> Build: SBT

Dependencies:

*   Apache Tika parser API - Higher-level parsing (includes non-PDF types) and content-type auto-detection.
*   Apache Pdfbox API - Parsing PDF.
*   Akka API - Web service/HTTP.
*   Sangria API - GraphQL.
*   MAANA Q RabbitMQ Service - To be notified when there are new files to process.
*   MAANA Q Service - addDocument (persist the extracted text) and addLink (from the file to the document).

Running outside Q
-----------------

### Requirements

> Git, Sbt, Java 8, Scala 2.11+

> Network access to Maana Q RabbitMQ and File/Document/Link services

### Environment Variable(s): 

**If not defined, default local docker settings are used.**

```ruby
  MAANA\_TEXT\_EXTRACTOR_PORT - default 8046

  PUBLIC\_BACKEND\_URI
     \- where dependent services are available (for addDocument, addLink, etc.).

  MAANA\_SERVICE\_REQ\_TIMEOUT\_IN_SEC - how long to wait for Q services to return: default 10 seconds

  RABBITMQ_ADDR

  RABBITMQ_PORT - usually 5672 for cluster Q (25672 for local Q - be sure to stop local rabbitmq)
```
**Security-Related Variable(s) against Secure Q**
```ruby
  REACT\_APP\_PORTAL\_AUTH\_DOMAIN  
  REACT\_APP\_PORTAL\_AUTH\_CLIENT_ID  
  REACT\_APP\_PORTAL\_AUTH\_CLIENT_SECRET  
  REACT\_APP\_PORTAL\_AUTH\_IDENTIFIER

  SAS_CREDENTIAL
```

### Local Execution

```ruby
git clone [ssh://git@bitbucket.corp.maana.io:7999/h4/h4.git](ssh://git@bitbucket.corp.maana.io:7999/h4/h4.git)

Follow instructions on local H4 service build/execution

```
