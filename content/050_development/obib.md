---
title: "OBIB"
date: 2019-11-21T12:50:21-07:00
weight: 11
---

## OBIB

[//]: # (TODO)

### Channels

#### OBIB Services

This channel is responsible for:

* Receive JSON messages via a REST API, defined in the "HTTP Listener" *source connector*;
* Route the messages to the appropriate destination connector according to the REST path;
* Transform the JSON messages to CDA Documents;
* Send the HL7 messages to the CDX WebServices using the **CDX Connector** Java Library;

As already mentioned, the *source connector* of the **OBIB Services** channel is an HTTP Listener that implements different services. These services are implemented by the Destination Set Filter *source connector* that routes the messages for the correct *destination connector* according to the *context path* of the HTTP request. Moreover, this channel has a couple of JavaScript *source connectors* to validate the Clinic's credentials and OBIB Connector version. Also, this HTTP Listener implements a custom HTTP authentication using the **clinicId** and **password** passed in the HTTP request header.

**OBIB Services** implements the following services using JavaScript Writer *destination connectors*.

* **Service Submit Document** is responsible for creating and submit CDA Documents.
  * It receives the messages in OBIB JSON format and converts them to CDX HLX using some JavaScript *transformers*.
  * The transformed document is sent to CDX using the method ```WSClientDocument.submitDocument()``` from the [CDXConnector library](#-CDX-Connector).
  * The CDX response is transformed into a JavaScript *Response Transformer* and is sent back to the OBIB client through the ```responseMessage``` variable. Also, the CDA Document is included in the OBIB response, both in JSON and XML formats after parsed by the [CDA Document Parser](#-CDA-Document-Parser) channel.
  * The metadata from both sent documents and responses from CDX are stored in the OBIB Database by [Document Storage](#-Document-Storage) channel.

* **Service List New Documents** is responsible for listing/pooling new CDA documents for a specific clinic.
  * It uses the method ```WSClientDocument.listNewDocuments()``` from the [CDXConnector library](#-CDX-Connector).
  * The CDX response is transformed into a JavaScript *Response Transformer* and is sent back to the OBIB client through the ```responseMessage``` variable.

* **Service Search Documents** is responsible for searching for documents by effective and event period, clinic id or document id.
  * It uses the method ```WSClientDocument.searchDocuments()``` from the [CDXConnector library](#-CDX-Connector).
  * The search parameters are mapped via Mapper *transformers*.
  * The CDX response is transformed into a JavaScript *Response Transformer* and is sent back to the OBIB client through the ```responseMessage``` variable.

* **Service Get Document** is responsible for getting a specific document by its id.
  * It uses the method ```WSClientDocument.getDocument()``` from the [CDXConnector library](#-CDX-Connector).
  * The query parameter is mapped via a Mapper *transformer*.
  * The CDX response is transformed into a JavaScript *Response Transformer* and is sent back to the OBIB client through the ```responseMessage``` variable.

* **Service List Clinics** is responsible for search/list clinics by clinic id, clinic name, and clinic address.
  * The search parameters are mapped via Mapper *transformers*.
  * The CDX response is transformed into a JavaScript *Response Transformer* and is sent back to the OBIB client through the ```responseMessage``` variable. Moreover, the Clinic Registry is parsed by the [CDA Registry Parser](#CDA-Registry-Parser) channel.

* **Service List Providers** is responsible for search/list providers by clinic id, provider id and provider name.
  * The search parameters are mapped via Mapper *transformers*.
  * The CDX response is transformed into a JavaScript *Response Transformer* and is sent back to the OBIB client through the ```responseMessage``` variable. Moreover, the Provider Registry is parsed by the [CDA Registry Parser](#CDA-Registry-Parser) channel.

* **Service OSP Support** is responsible for process (error) notifications from the OBIB clients using the [OSP Support](#-OSP-Support) channel.
  * A JavaScript *transformer* encodes the message for the [OSP Support](#-OSP-Support) channel.
  * A JavaScript *response transformer* generate a response for the OBIB client.

* **Service Distribution Status** is responsible for searching/requesting the distribution status of a document from CDX.
  * The search/request parameters are mapped via Mapper *transformers*.
  * The CDX response is transformed into a JavaScript *Response Transformer* and is sent back to the OBIB client through the ```responseMessage``` variable.

#### CDA Document Parser

This channel is responsible for converting CDA Documents (CDX HL7) to OBIB JSON Documents.

Its *source connector* is a Channel Reader and it has only one *destination connector*, named **Parse CDA Document** which is a Javascript Writer. **Parse CDA Document** stores the transformed message (JSON) into the response map, and this message is afterwards returned by the *source connector*.

The messages are transformed by the **Parse CDA Document's** *transformers*. Each transformer is responsible for transform a section of the document.

#### CDA Registry Parser

This channel is responsible for converting the CDX XML registry messages (clinics and providers) into OBIB JSON messages.

Its *source connector* is a Channel Reader and it has two *destination connectors*: **Parse Clinic Registry** and **Parse Provider Registry** which are Javascript Writers. Both connectors store the transformed message (JSON) into the response map, and this message is afterwards returned by the *source connector*.

To select which *destination connector* is responsible for a message, the *source connector* has two *transformers* that act as Destination Set Filters that route the messages according to the value of **//subject1/assignedEntity/code/@code** of the XML.

**Parse Clinic Registry** transforms the Clinic Registry messages using three *transformers*. Also, it has a filter to get only messages in which the clinic is able to receive a specific document type (e-Referrals).

**Parse Provider Registry** transforms the Provider Registry messages using two *transformers*. It also has a filter to get only messages in which the clinic is able to receive a specific document type (e-Referrals).

#### OSP Support

This channel is responsible for receive and store (error) messages, and forward them via email.

Its *source connector* is a Channel Reader and to handle the messages **OSP Support** has two *destination connectors*, which are:

* **Store Error Message** is a Database Writer and it is responsible for persisting the message in the OBIB Database.

* **Send Notification Email** is a SMTP Sender and it is responsible for sending the messages via e-mail for the registered recipients.

#### Document Storage

This channel is responsible for storing the metadata of the documents sent to CDX and the metadata of the CDX responses in the OBIB database.

Its *source connector* is a Channel Reader and it has three Database Writer *destination connectors* to store the different messages' metadata: **Store CDA Document**, **Store CDA Response** and **Store CDA Attachment**.

To select which *destination connector* is responsible for a message, the *source connector* has three *transformers* that act as Destination Set Filters that route the messages according to the type of the message (tag **messageType**).

### Global Scripts

OBIB only utilizes the *Deploy* global script, that executes once for each deploys or redeploy task. This script loads information from the properties file, and from the OBIB Database, and stores this information in the global map to be accessible by the channels.

### Code Templates

OBIB has a number of *code templates*, which are functions that can be used across multiple channels. These *code templates* are divided into two *code template libraries* just to organization purpose. The **OBIB** library contains general use scripts and the **CDA** library contains scripts to parse, format and build CDA message snippets.

## Connectors

### CDX Connector

CDX Connector is a Java library responsible for connecting to CDX Web Services. Most of the code necessary to communicate with the Web Services is generated from the Web Services definitions using Maven plugins.

The following plugins are utilized to automate the WS client code generation:

* [Keytool Maven Plugin](https://www.mojohaus.org/keytool/keytool-maven-plugin/), import the CDX certificates into a PFX keystore which is utilized to download the WSDL from CDX.
* [WSDLGet Maven Plugin](https://github.com/dmn1k/wsdlget-maven-plugin), download the WSDL files from CDX.
* [Apache CXF Maven Plugin](https://cxf.apache.org/docs/maven-cxf-codegen-plugin-wsdl-to-java.html), generate java code from WSDL files.

#### Code structure

The classes **WSClientDocument.java**, **WSClientClinic.java**, **WSClientProvider.java** contain the public methods that call the CDX WS methods.

The package **ca.uvic.leadlab.cdxconnector.messages** contain builders and object factories to create WS messages.

The folder **src/main/resources/wsdl** stores the WSDL and XSD files downloaded from CDX, and utilized to generate the WS client code.

{{% notice info %}}
Due to some limitations in the Apache CXF and incompatibilities with the CDX WS definitions, some "hacks" in the WSDL and XSD files were necessary to generate Java code. All "hacks" are commented with the tag ```HACK:```.
{{% /notice %}}

#### Command line WS Client

When the CDX Connector jar file is generated, it also generates a **jar-test** that can be utilized for submitting CDA XML documents via the command line. See the CDX Connector [README.md](https://github.com/simbioses/OBIB/tree/master/cdxconnector#submit-document-client) for more details.

### OBIB Connector

OBIB Connector is a Java library responsible for connecting to OBIB REST services.

#### Code structure

* Package **facades** define fluent interfaces to submit and receive documents, look for registries (clinics and providers), and execute supportive operations in OBIB.

* Package **impl** implements the interfaces defined in **facades**.

* Package **models** define the class structure of the OBIB JSON Document. These classes are utilized to generate the JSON messages sent to the OBIB.

* Package **rest** implements the client methods for the OBIB REST services. The REST client utilizes the setting in the **obibconnector.properties** file to communicate with the OBIB.

## OBIB VM

[//]: # (TODO)