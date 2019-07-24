# OSCAR CDX Interoperability User Manual

# Introduction

The HL7 Clinical Document Architecture (CDA) is a standard to communicate clinical documents electronically between provider systems.
The Provincial Health Services Authority (PHSA) has partnered with other health authorities to establish CDA-based interoperability between clinical information systems, including primary care EMRs. This initiative uses the Clinical Document eXchange (CDX) communication system (https://bccdx.ca). The PHSA has worked with EMR vendors to implement CDX-based interoperability. OSCAR BC has created a first version of OSCAR EMR that is CDX interoperable. This first version is capable of 

1. **receiving** and displaying any of the approved types of CDX documents, e.g., eReferrals, Progress Notes, Patient Summaries, Discharge Summaries, General Purpose Notifications, Lab Results, etc.
2. and **sending** eReferral requests to specialists connected to the CDX system.
# Administration

Note: The contents of this section is mainly for admin users and OSPs.

## Configuration

The connection with the CDX system can be configured and checked using OSCAR’s admin panel.


> Admin → Integration → Clinical Data eXchange (CDX)


![](https://paper-attachments.dropbox.com/s_D8F55B926E14BC491F2DAD18D930CB06AD57C72BB921C2ECDB6B0AA89F2D0027_1558045246712_image.png)

- **OBIB URL** specifies the URL of the Oscar Bidirectional Interoperability Bus (OBIB)
- **Clinic OID** specifies the ID assigned to the clinic by CDX.
- **Automatic Import Enabled/Disabled** specifies whether OSCAR will automatically download new documents.
- **Polling Interval** specifies the time interval between automatic downloads (in minutes)
- **Save** button makes the settings persistent

The **CDX Status** tab indicates whether OSCAR is connected with an OBIB.

## Manual document import

The CDX Admin panel allows administrators to manually download documents from the CDX system. 

*(Note: manual download is used only for testing and to recover from errors.)*

**Manual download of “new” documents**
The CDX document distribution system considers documents as “new”, if and only if they have not yet been downloaded from the CDX system.

OSCAR EMR can be configured to download “new” documents automatically. Hence, manual download of “new” documents is not needed during normal operations. However, it may be useful for testing purposes.

The Admin panel indicates the number of “new” documents waiting for a clinic. They can be downloaded manually using the `import` button (only visible if “new” documents are available).

![](https://paper-attachments.dropbox.com/s_D8F55B926E14BC491F2DAD18D930CB06AD57C72BB921C2ECDB6B0AA89F2D0027_1558051017883_image.png)


**Manual download of “old” documents**
The CDX document distribution system considers documents as “old” if the EMR system has downloaded them already. Still, the CDX system retains downloaded documents for an unspecified time, so that they can be downloaded again.

*Why import a document that has already been downloaded?*

This should be necessary only in two types of (exceptional) cases:

1. **Import Errors**. If an error occurred during document import, the document is considered delivered (“old”) from the perspective of the CDX system. However, the document has not successfully been imported by the EMR. Once the problem has been diagnosed and fixed, the admin can use the CDX Admin panel to download the document(s) that could not be imported before (see below).
2. **Undo Document Deletion**. EMR users have the ability to delete imported documents if they are convinced that the document was delivered in error. The CDX Admin panel allows a user to effectively undo such a manual deletion if it turns out that the document was deleted in error (see below).

The CDX Admin panel has a table showing those documents available from the CDX system that are not currently imported in the EMR. They can selectively be imported.

The figure below shows the CDX Admin panel indicating that there was one unsuccessful download and one document that was deleted. The user selected the unsuccessful download and can press the “*Import selected documents*” button to reattempt the import.

![](https://paper-attachments.dropbox.com/s_D8F55B926E14BC491F2DAD18D930CB06AD57C72BB921C2ECDB6B0AA89F2D0027_1558054982778_image.png)

# Receiving and Routing
## Routing to the “Inbox”

CDX documents may identify several *primary and secondary recipients* (roughly analogous to the “To:” and “Cc:” fields in email.) CDX documents may also not specify any recipient provider - but just a recipient clinic. 

OSCAR’s CDX import engine uses the following logic to route incoming CDX documents to provider inboxes:

1. (Step 1) route the document to the Inbox of any provider with the same MSP number as any of the recipients named in the document.
2. (Step 2) if Step 1 did not result in any provider matches *and* if the patient named in the document could be matched to a demographic record in the EMR *then* route the document to the most responsible provider (MRP) of the matched patient (if available).
3. (Step 3) if Steps 1 and 2 did not result in any provider matches, the document is considered “*unclaimed*" and can be retrieved by searching for “unclaimed” documents in the Inbox.


![Click “Search” to find unclaimed documents](https://paper-attachments.dropbox.com/s_D8F55B926E14BC491F2DAD18D930CB06AD57C72BB921C2ECDB6B0AA89F2D0027_1558112387277_image.png)
![](https://paper-attachments.dropbox.com/s_D8F55B926E14BC491F2DAD18D930CB06AD57C72BB921C2ECDB6B0AA89F2D0027_1558112257391_image.png)



## 
![Example search result showing an “unclaimed” document](https://paper-attachments.dropbox.com/s_D8F55B926E14BC491F2DAD18D930CB06AD57C72BB921C2ECDB6B0AA89F2D0027_1558114144052_image.png)

## Associating incoming documents with patients

The CDX import engine attempts to automatically match incoming documents to patient demographic records in the EMR. This is done using *4-point matching* as follows:

        - last name
        - gender
        - health number
        - gender

Documents for which the above 4-point match fails are shown as “not assigned” in the Inbox view (see above).

## Information shown in Inbox view
- Similar to other documents and labs, the Inbox shows **patient name, number and gender**.
- **Result status** is **not** currently shown. 
    - (⚠️ if the CDX document is a lab result, the result status is NOT displayed in the Inbox view. Consequently, the colour is NOT changed to red for an abnormal result.)
- The “**Date of Test**” column shows the **Observation Date**, followed by the time the document was authored (**Authoring Date**).
- The “**Order Priority**” and “**Requesting Client**” columns are **not** currently used for CDX documents.
- The “**Discipline**” column identifies the type of CDX document received.
- The “**Report status**” column displays the status of the document, which can be either
    - active (marks a preliminary document that may be updated),
    - completed (marks a final document), or
    - cancelled (marks a document that cancels and earlier one)
- The “**Ack**” column marks the acknowledgement status of a document.
![](https://paper-attachments.dropbox.com/s_D8F55B926E14BC491F2DAD18D930CB06AD57C72BB921C2ECDB6B0AA89F2D0027_1558123878547_image.png)

## Viewing and Acknowledging CDX Documents

As with other Inbox items, clicking on a CDX Document will open up a viewer window that shows the content of the document, including potential attachments (e.g., PDF documents, images, etc.). The CDX Document Viewer provides the familiar functionality of acknowledging a document, flagging / messaging other providers, etc. (see below).


![](https://paper-attachments.dropbox.com/s_D8F55B926E14BC491F2DAD18D930CB06AD57C72BB921C2ECDB6B0AA89F2D0027_1558125468209_image.png)



## Demographic data consistency warning

Even if the CDX Import Engine was able to positively match the patient named in the document with a demographic record in the EMR (using 4 point matching, see above), the Import Engine may alert the user about inconsistencies between the demographic data in the document and in the EMR (see below). 

![](https://paper-attachments.dropbox.com/s_D8F55B926E14BC491F2DAD18D930CB06AD57C72BB921C2ECDB6B0AA89F2D0027_1558125081142_image.png)


The clinician may use this warning as a motivation to review and possibly update the patient demographic record. 

Note: 

- the update of demographic data is currently not automated
- there is currently no way to “dismiss” the warning. It will remain in the Inbox viewer even after the demographic record has been updated. (this will be improved in the next version)
## Manually linking documents to existing patient demographics

The CDX Viewer shows a warning if the Import Engine failed to automatically match the document to a patient demographic record using the 4-point matching (last name, DOB, sex, HIN).
In this case, the user can manually search the EMR demographic records and link the document to a patient (see “Demographic Search” input and “Link” button below).
 

![](https://paper-attachments.dropbox.com/s_D8F55B926E14BC491F2DAD18D930CB06AD57C72BB921C2ECDB6B0AA89F2D0027_1558127547508_image.png)

## Creating a new Demographic

If the CDX document refers to a new patient (i.e., a patient that does not have a demographic record in the EMR), the user can create a new Demographic Record based on the information provided in the CDX document (see “New” button”). The CDX Document will be linked automatically to this newly created Demographic record.

## Deleting a CDX Document that was received in error

Finally, the user may delete a CDX document that was received in error, e.g., if the patient named on the document is not (and will not be) a client of the clinic. (see “Delete” button)
Note: Documents that were deleted in error can be restored by an Admin (see Administration above).


## Viewing documents with different versions

It is possible to receive different versions of the same logical document. These documents will appear as separate entries in the Inbox. The CDX Viewer alerts the user that different versions are available and whether the user views the most up-to-date version. The user can switch between different versions.

![Example viewing an outdated version of a document](https://paper-attachments.dropbox.com/s_D8F55B926E14BC491F2DAD18D930CB06AD57C72BB921C2ECDB6B0AA89F2D0027_1558130239728_image.png)

![Viewer indicating that user sees the latest version](https://paper-attachments.dropbox.com/s_D8F55B926E14BC491F2DAD18D930CB06AD57C72BB921C2ECDB6B0AA89F2D0027_1558130319998_image.png)

## Related documents

CDX documents may also be related in ways other than versioning:

- a document may have a “parent document”
- documents may belong to the same “set of documents”
- a document may have been sent “in fulfillment of” another document (e.g., a consult not document may have been sent in fulfillment of an referral request document)

Any related CDX documents are displayed at the bottom of the CDX Viewer window:

![](https://paper-attachments.dropbox.com/s_D8F55B926E14BC491F2DAD18D930CB06AD57C72BB921C2ECDB6B0AA89F2D0027_1558130889255_image.png)

# CDX Documents as part of a Patient’s eChart

CDX documents appear under “Documents” in the patient’s eChart. For example, the General Lab Report, History & Physical Note, and Consult Note documents in the chart below are CDX documents:

![](https://paper-attachments.dropbox.com/s_D8F55B926E14BC491F2DAD18D930CB06AD57C72BB921C2ECDB6B0AA89F2D0027_1558131674577_image.png)


Note: in future versions of the CDX integration, Oscar may “sort” CDX document types more appropriately, e.g., lab results under “Lab Results” and consultation notes under “Consultations”.

Documents can be viewed by clicking on them.

# Sending CDX documents

The current OSCAR version can send only one type of CDX documents, namely e-Referral requests. Other types of CDX documents will be supported in the future.

## Sending e-Referral Requests

⚠️ The term “referral” is used to align with provincial standard terminology. OSCAR uses the term “consultation”. They can be considered synonymous in the following.

**Adding a CDX capable specialist**
E-referrals can only be sent to specialists that are connected to the CDX system. The “Add Specialist” UI has been extended to add CDX-capable specialists.

![](https://paper-attachments.dropbox.com/s_D8F55B926E14BC491F2DAD18D930CB06AD57C72BB921C2ECDB6B0AA89F2D0027_1558132363494_image.png)


The new dialogue allows the user to search the CDX provider registry for providers to add to OSCAR. The search is based on any letters and returns all providers with these letters in their names.

![](https://paper-attachments.dropbox.com/s_D8F55B926E14BC491F2DAD18D930CB06AD57C72BB921C2ECDB6B0AA89F2D0027_1558132543256_image.png)


Similarly, specialists can be updated.
⚠️ At this point, the CDX system does not provide the speciality of providers returned from its registry. The speciality needs to be added manually:

![](https://paper-attachments.dropbox.com/s_D8F55B926E14BC491F2DAD18D930CB06AD57C72BB921C2ECDB6B0AA89F2D0027_1558132847335_image.png)


ℹ️ There are plans to improve the functionality around managing specialists in future versions of OSCAR.

**Creating and sending an e-Referral**
E-referrals are created in the usual way (like normal “consultations”) . Of course, it is important to select a CDX-capable specialist as the “Consultant”. These specialists are clearly marked in the dropdown box with “(CDX)” (see below):

![](https://paper-attachments.dropbox.com/s_D8F55B926E14BC491F2DAD18D930CB06AD57C72BB921C2ECDB6B0AA89F2D0027_1558133350031_image.png)


Attachments can be added to the e-Referral (see left side of window). Once the data on the referral has been entered completely, the e-referral can be sent using the “Submit and Save CDX e-Referral” button (top right).

**Checking the status of an e-Referral and viewing it**
The user can validate that an e-referral was indeed sent by viewing the left side bar of the e-referral. It mentions the date and time the referral was sent.

![](https://paper-attachments.dropbox.com/s_D8F55B926E14BC491F2DAD18D930CB06AD57C72BB921C2ECDB6B0AA89F2D0027_1558134148196_image.png)


Moreover, the user can view the information that was sent via CDX by clicking on the link.

![](https://paper-attachments.dropbox.com/s_D8F55B926E14BC491F2DAD18D930CB06AD57C72BB921C2ECDB6B0AA89F2D0027_1558134271046_image.png)

# Summary

This document describes the functionality added to OSCAR for its Version 1 of CDX interoperability. There are of course several places where the existing implementation can be improved. Such improvements will be shipped in future versions of the software.


