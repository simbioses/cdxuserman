---
title: "Configuration"
date: 2019-06-14T11:37:29-07:00
weight: 3
---

The CDX systems needs to be connected to the OBIB (*Oscar Bidirectional Interoperability Bus*) exchange data. Users with role "admin" can check this in the CDX user interface in OSCAR's Admin panel.

> Admin → Integration → Clinical Data eXchange (CDX)

The **CDX Status** tab indicates whether OSCAR is connected with an OBIB.

If the CDX Status tab indicates *The OBIB is not connected*, please contact your OSCAR Service Provider.

{{< figure title="CDX Configuration" src="/images/image001.png" width="100%">}}

Administrators can configure the OBIB connection:

- **OBIB URL** specifies the URL of the Oscar Bidirectional Interoperability Bus (OBIB)
- **Clinic OID** specifies the ID assigned to the clinic by CDX. This is a read only value sourced from the `obibconnector.properties` file installed when OSCAR is configured for CDX.
- **Automatic Import Enabled/Disabled** specifies whether OSCAR will automatically download new documents.
- **Polling Interval** specifies the time interval between automatic downloads (in minutes)
- **Save** button makes the settings persistent
