---
title: "OSCAR Installation"
date: 2019-06-14T11:37:29-07:00
weight: 6
---

## OBIB Connector Credentials
The [Clinic Registration] ({{< ref clinic_registration >}}) process creates two files `obibconnector.properties` and `obibconnector.keystore`that need to be placed in $CATALINA_HOME on the OSCAR server. For example:

```
$ sudo cp obibconnector.* $CATALINA_HOME/
```

Then, change the owner of the files to allow tomcat to read them, and change their permissions to secure them from unauthorized read/write.

```
$ chown tomcat7 obibconnector.*

$ chmod 400 obibconnector.*
```

## OSCAR UI Properties

It is recommended to set the following properties in the `oscar.properties` file:

| Property | Description |
| -------- | ----------- |
| `hide_ConReport_link=yes` | This hides the consultation report menu item to avoid confusion with the CDX e-referrals. This functionality is not used in B.C. |
| `hide_add_specialist_edata=yes` | This hides the specialist edata UI items to avoid confusion with the BC e-referal solution using CDX |
| `hide_eConsult_link=yes` | This hides the eConsult link to avoid confusion with the BC e-referal solution using CDX |
