---
title: "Associating incoming documents with patients"
date: 2019-06-14T11:37:29-07:00
weight: 9
draft: true
---

The CDX import engine attempts to automatically match incoming documents to patient demographic records in the EMR. This is done using *4-point matching* as follows:

        - last name
        - gender
        - health number
        - gender

Documents for which the above 4-point match fails are shown as “not assigned” in the Inbox view (see above).
