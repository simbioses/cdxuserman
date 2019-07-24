---
title: "Routing to the Inbox"
date: 2019-06-14T11:37:29-07:00
weight: 3
draft: true
---


CDX documents may identify several *primary and secondary recipients* (roughly analogous to the “To:” and “Cc:” fields in email.) CDX documents may also not specify any recipient provider - but just a recipient clinic.

OSCAR’s CDX import engine uses the following logic to route incoming CDX documents to provider inboxes:

1. (Step 1) route the document to the Inbox of any provider with the same MSP number as any of the recipients named in the document.
2. (Step 2) if Step 1 did not result in any provider matches *and* if the patient named in the document could be matched to a demographic record in the EMR *then* route the document to the most responsible provider (MRP) of the matched patient (if available).
3. (Step 3) if Steps 1 and 2 did not result in any provider matches, the document is considered “*unclaimed*" and can be retrieved by searching for “unclaimed” documents in the Inbox.


![Click “Search” to find unclaimed documents](https://paper-attachments.dropbox.com/s_D8F55B926E14BC491F2DAD18D930CB06AD57C72BB921C2ECDB6B0AA89F2D0027_1558112387277_image.png)

![](https://paper-attachments.dropbox.com/s_D8F55B926E14BC491F2DAD18D930CB06AD57C72BB921C2ECDB6B0AA89F2D0027_1558112257391_image.png)

![Example search result showing an “unclaimed” document](https://paper-attachments.dropbox.com/s_D8F55B926E14BC491F2DAD18D930CB06AD57C72BB921C2ECDB6B0AA89F2D0027_1558114144052_image.png)
