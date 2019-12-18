---
title: "OBIB"
date: 2019-11-21T12:50:21-07:00
weight: 11
---

The *Oscar Bidirectional Interoperability Bus* (**OBIB**) provides a set of services to achieve interoperability between Oscar and CDX. These services are implemented through Mirth Connect channels and a couple of Web Services and RESTful clients. In addition, OBIB is embedeed in a Virtual Machine (VM) defined by Vagrant.

## OBIB VM

The OBIB VM is an *Ubuntu 18.04* VM build and managed using [Vagrant](https://www.vagrantup.com/). When deployed, **OBIB VM** contains all software necessary to execute the OBIB services.

### Configuration Files and Scripts

* **Vagrantfile** is the Vagrant definition file for the virtual machine and its configurations, such as memory, network type, IP address, forwarded ports, and shared folders.

* **mirth_connect.sh** contains the configurations needed to install the software within the VM and deploy the OBIB Services.

* **install.sh** is executed automatically during the VM installation and it is responsible for install and configure all software required by OBIB. When executed the script:
  * Update the Operating System - [Ubuntu](http://releases.ubuntu.com/18.04/);
  * Install the [OpenJDK 8](https://openjdk.java.net/), required by Mirth Connect;
  * Install and configure the [Nginx](https://www.nginx.com/), that works as a reverse proxy and implements SSL/TLS;
  * Install additional XML tools necessary by other scripts;
  * Install and configure the [MariaDB Server](https://mariadb.org/), utilized by MirthConnect and OBIB;
  * Install and configure the [Mirth Connect](http://www.mirthcorp.com/community/wiki/display/mirth/Home)

* **deploy.sh** is executed as a [Vagrant provision](https://www.vagrantup.com/docs/cli/provision.html) and it is responsible for deploy and update OBIB within Mirth Connect. **deploy.sh** updates the database by running the script *./dbscripts/OBIB_DB_update.sql*, updates the Mirth Connect's resources, global scripts, channels and code template libraries, and redeploy all Mirth Connect's channels.
{{% notice note %}}
See the section [OBIB Installation]({{< ref "/030_installation/obib_install.md#deploy-the-obib-channels-automatically" >}}) for instruction on how to run **deploy.sh**.
{{% /notice %}}

* **register.sh** is an administrative script responsible for the clinics' management (register, unregister, and verification).
{{% notice note %}}
Instructions on how to use **register.sh** are in the section [Clinic Registration]({{< ref "/030_installation/clinic_registration.md#2-registering-the-clinic-in-obib" >}}) of this manual.
{{% /notice %}}
{{% notice info %}}
For security reasons, this script should be executed from within the VM.
{{% /notice %}}

* **gen_obib_certs.sh** is a helper script that generates a self-signed SSL/TLS CA certificate for OBIB. This certificate is utilized to generate the clinics' certificates, which are utilized by OBIB Connector to access the OBIB Services.
{{% notice info %}}
For security reasons, this script should be executed from within the VM.
{{% /notice %}}

## OBIB Service

### Channels

#### OBIB Services

This channel is responsible for:

* Receive JSON messages via a REST API, defined in the "HTTP Listener" *source connector*;
* Route the messages to the appropriate destination connector according to the REST path;
* Transform the JSON messages to CDA Documents;
* Send the HL7 messages to the CDX WebServices using the **CDX Connector** Java Library;

As already mentioned, the *source connector* of the **OBIB Services** channel is an HTTP Listener that implements different services. These services are implemented by the Destination Set Filter *source connector* that routes the messages for the correct *destination connector* according to the *context path* of the HTTP request. Moreover, this channel has a couple of JavaScript *source connectors* to validate the Clinic's credentials and OBIB Connector version. Also, this HTTP Listener implements a custom HTTP authentication using the **clinicId** and **password** which are sent in the HTTP request header.

**OBIB Services** implements the following services using JavaScript Writer *destination connectors*.

* **Service Submit Document** is responsible for creating and submit CDA Documents.
  * It receives the messages in OBIB JSON format and converts them to CDX HLX using some JavaScript *transformers*.
  * The transformed document is sent to CDX using the method ```WSClientDocument.submitDocument()``` from the [CDXConnector library]({{< ref "#cdx-connector" >}}).
  * The CDX response is transformed into a JavaScript *Response Transformer* and is sent back to the OBIB client through the ```responseMessage``` variable. Also, the CDA Document is included in the OBIB response, both in JSON and XML formats after parsed by the [CDA Document Parser]({{< ref "#cda-document-parser" >}}) channel.
  * The metadata from both sent documents and responses from CDX are stored in the OBIB Database by [Document Storage]({{< ref "#document-storage" >}}) channel.

* **Service List New Documents** is responsible for listing/pooling new CDA documents for a specific clinic. It uses the method ```WSClientDocument.listNewDocuments()``` from the [CDXConnector library]({{< ref "#cdx-connector" >}}).
  * The CDX response is transformed into a JavaScript *Response Transformer* and is sent back to the OBIB client through the ```responseMessage``` variable.

* **Service Search Documents** is responsible for searching for documents by effective and event period, clinic id or document id. It uses the method ```WSClientDocument.searchDocuments()``` from the [CDXConnector library]({{< ref "#cdx-connector" >}}).
  * The search parameters are mapped via Mapper *transformers*.
  * The CDX response is transformed into a JavaScript *Response Transformer* and is sent back to the OBIB client through the ```responseMessage``` variable.

* **Service Get Document** is responsible for getting a specific document by its id. It uses the method ```WSClientDocument.getDocument()``` from the [CDXConnector library]({{< ref "#cdx-connector" >}}).
  * The query parameter is mapped via a Mapper *transformer*.
  * The CDX response is transformed into a JavaScript *Response Transformer* and is sent back to the OBIB client through the ```responseMessage``` variable.

* **Service List Clinics** is responsible for search/list clinics by clinic id, clinic name, and clinic address. It uses the method ```WSClientClinic.listClinics()``` from the [CDXConnector library]({{< ref "#cdx-connector" >}}).
  * The search parameters are mapped via Mapper *transformers*.
  * The CDX response is transformed into a JavaScript *Response Transformer* and is sent back to the OBIB client through the ```responseMessage``` variable. Moreover, the Clinic Registry is parsed by the [CDA Registry Parser]({{< ref "#cda-registry-parser" >}}) channel.

* **Service List Providers** is responsible for search/list providers by clinic id, provider id and provider name. It uses the method ```WSClientProvider.listProviders()``` from the [CDXConnector library]({{< ref "#cdx-connector" >}}).
  * The search parameters are mapped via Mapper *transformers*.
  * The CDX response is transformed into a JavaScript *Response Transformer* and is sent back to the OBIB client through the ```responseMessage``` variable. Moreover, the Provider Registry is parsed by the [CDA Registry Parser]({{< ref "#cda-registry-parser" >}}) channel.

* **Service OSP Support** is responsible for process (error) notifications from the OBIB clients using the [OSP Support]({{< ref "#osp-support" >}}) channel.
  * A JavaScript *transformer* encodes the message for the [OSP Support]({{< ref "#osp-support" >}}) channel.
  * A JavaScript *response transformer* generate a response for the OBIB client.

* **Service Distribution Status** is responsible for searching/requesting the distribution status of a document from CDX. It uses the method ```WSClientDocument.getDistributionStatus()``` from the [CDXConnector library]({{< ref "#cdx-connector" >}}).
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

Its *source connector* is a Channel Reader and to handle the messages **OSP Support** has two chained *destination connectors*, which are executed in sequence. The channels are:

* **Store Error Message** is a Database Writer and it is responsible for persisting the message in the OBIB Database.

* **Send Notification Email** is a SMTP Sender and it is responsible for sending the messages via e-mail for the registered recipients. To test this service, [FakeSMTP](http://nilhcem.com/FakeSMTP/) is being used as a mock SMTP local server. It must be run separately, and the received emails are saved in files locally.

#### Document Storage

This channel is responsible for storing the metadata of the documents sent to CDX and the metadata of the CDX responses in the OBIB database.

Its *source connector* is a Channel Reader and it has three Database Writer *destination connectors* to store the different messages' metadata: **Store CDA Document**, **Store CDA Response** and **Store CDA Attachment**.

To select which *destination connector* is responsible for a message, the *source connector* has three *transformers* that act as Destination Set Filters that route the messages according to the type of the message (tag **messageType**).

### Global Scripts

OBIB only utilizes the *Deploy* global script, that executes once for each deploys or redeploy task. This script loads information from the properties file, and the OBIB Database, and stores this information in the global map to be accessible by the channels.

### Code Templates

OBIB has a number of *code templates*, which are functions that can be used across multiple channels. These *code templates* are divided into two *code template libraries* just to organization purpose. The **OBIB** library contains general use scripts and the **CDA** library contains scripts to parse, format and build CDA message snippets.

### Development Environment

Since OBIB is implemented in Mirth Connect, the easiest way to maintain code is by using the *Mirth Connect Administrator*. To execute the *Mirth Connect Administrator* it is necessary download an *Administrator Launcher*, which is available in the Mirth Connect Launch Page (see figure below), or in the [Nextgen's download page](https://www.nextgen.com/products-and-services/nextgen-connect-integration-engine-downloads).

{{< figure title="Mirth Connect Launch Page" src="/images/mirth_connect_launch_page.png" width="50%">}}

{{% notice note %}}
See the section [OBIB Installation]({{< ref "/030_installation/obib_install.md#deploy-the-obib-channels-manually" >}}) for instruction on how to login into **Mirth Connect Administrator**.
{{% /notice %}}

{{% notice info %}}
The easiest way to use **Mirth Connect Administrator** is download it from the Nextgen's download page and run it by calling the ```./launcher``` script.
{{% /notice %}}

## Connectors (Services Clients)

### CDX Connector

CDX Connector is a Java library responsible for connecting to CDX Web Services. Most of the code necessary to communicate with the Web Services is generated from the Web Services definitions using Maven plugins.

The following plugins are utilized to automate the WS client code generation:

* [Keytool Maven Plugin](https://www.mojohaus.org/keytool/keytool-maven-plugin/), import the CDX certificates into a PFX keystore which is utilized to download the WSDL from CDX.
* [WSDLGet Maven Plugin](https://github.com/dmn1k/wsdlget-maven-plugin), download the WSDL files from CDX.
* [Apache CXF Maven Plugin](https://cxf.apache.org/docs/maven-cxf-codegen-plugin-wsdl-to-java.html), generate java code from WSDL files.

#### Code structure

The classes **WSClientDocument.java**, **WSClientClinic.java**, **WSClientProvider.java** contain the public methods that call the CDX WS methods.

The package **ca.uvic.leadlab.cdxconnector.messages** contains builders and object factories to create WS messages.

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

#### Testing

{{% notice info %}}
The OBIB Connector tests connect to OBIB using SSL/TLS. Thus, when a new OBIB is deployed in the development environment it is necessary copy the new clinic keystore to the folder /certs in obibconnector and run the maven task 'package';
{{% /notice %}}
