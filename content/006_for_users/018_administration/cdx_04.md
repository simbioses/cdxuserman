---
title: "Manual Document Import"
date: 2019-06-14T11:37:29-07:00
weight: 5
---

The CDX Admin panel allows administrators to manually download documents from the CDX system.

{{% notice info %}}
<p>
Manual Document download is used only for testing and to recover from errors.
</p>
{{% /notice %}}

## Manual download of “new” documents

The CDX document distribution system considers documents as “new”, if and only if they have not yet been downloaded from the CDX system.

OSCAR EMR can be configured to download “new” documents automatically. Hence, manual download of “new” documents is not needed during normal operations. However, it may be useful for testing purposes.

The Admin panel indicates the number of “new” documents waiting for a clinic. They can be downloaded manually using the `import` button (only visible if “new” documents are available).

![](https://paper-attachments.dropbox.com/s_D8F55B926E14BC491F2DAD18D930CB06AD57C72BB921C2ECDB6B0AA89F2D0027_1558051017883_image.png)


## Manual download of “old” documents

The CDX document distribution system considers documents as “old” if the EMR system has downloaded them already. Still, the CDX system retains downloaded documents for an unspecified time, so that they can be downloaded again.

*Why import a document that has already been downloaded?*

This should be necessary only in two types of (exceptional) cases:

1. **Import Errors**. If an error occurred during document import, the document is considered delivered (“old”) from the perspective of the CDX system. However, the document has not successfully been imported by the EMR. Once the problem has been diagnosed and fixed, the admin can use the CDX Admin panel to download the document(s) that could not be imported before (see below).
2. **Undo Document Deletion**. EMR users have the ability to delete imported documents if they are convinced that the document was delivered in error. The CDX Admin panel allows a user to effectively undo such a manual deletion if it turns out that the document was deleted in error (see below).

The CDX Admin panel has a table showing those documents available from the CDX system that are not currently imported in the EMR. They can selectively be imported.

The figure below shows the CDX Admin panel indicating that there was one unsuccessful download and one document that was deleted. The user selected the unsuccessful download and can press the “*Import selected documents*” button to reattempt the import.

![](https://paper-attachments.dropbox.com/s_D8F55B926E14BC491F2DAD18D930CB06AD57C72BB921C2ECDB6B0AA89F2D0027_1558054982778_image.png)
