---
title: "Architecture"
date: 2019-09-06T17:47:51-07:00
weight: 3
---

The diagram below shows a component & connector view of the implemented architecture. OSCAR instances are connected to the CDX infrastructure via the *Oscar Bidirectional Interoperability Bus* (**OBIB**).

The functionality required to interact with the OBIB is encapsulated in the **OBIB Connector** component which is made available as a Java archive (JAR). The OBIB connection is configured by means of two artifacts on the OSCAR server: an *obibconnector properties* file and an *obibconnector keystore* file (see [deployment] ({{< ref OSCAR_install >}})).

{{< figure title="Architecture" src="/images/architecture.png" width="100%">}}

The **OBIB Connector** interfaces with the **OBIB** using a secured REST API that is provided by the interface engine **MirthConnect**. Several **CDX Channels** are deployed on **MirthConnect** to generate and receive CDX documents.

MirthConnect interfaces with the CDX infrastructure via secured SOAP interfaces. That connection is implemented in the **CDX Connector** component.
