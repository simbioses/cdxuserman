---
title: "Sending"
date: 2019-06-14T11:37:29-07:00
weight: 6
---

CDX Composer can be used to send different CDX documents like information request, progress note and patient summary from a single interface. 


{{< figure title="CDX Composer" src="/images/CdxComposer.png" width="90%">}}

#### Patient
Patients can be directly searched by their first and last name and added to the document. 

#### Primary Recipient(s) and Secondary Recipient(s)
The CDX infrastructure has a **provider registry**, which contains address information about all CDX-capable providers and clinics.

Even though, OSCAR has traditionally kept its own database of specialists. The CDX Composer allows users to query the CDX provider registry and add CDX capable providers directly into the document without using the OSCAR database.
By clicking "Add", Primary Recipient(s) and Secondary Recipient(s) can be directly searched by Name from the CDX provider registry and added to the document.
User can also remove the selected Primary Recipient(s) and Secondary Recipient(s) from the document by click the "x" button before the names.
{{< figure title="Modal for adding recipients" src="/images/Recipients.png" width="70%">}}

{{% notice info %}}
Some providers may work for more than one clinic. In this case, the user can select the specific clinics to direct the CDX document to.
{{% /notice %}}

#### Message Type
Message type will be "In response to {document type}" if user is replying to an incoming document, "New" otherwise. 

#### Document Type
CDX Composer allows sending information request, patient summary and progress note. A user can select the type of document from the drop down.

#### Content
CDX Composer allows sending free text with the document and provides a text area for that.

#### Other important info
Other important info like family history, medical history, risk factors and allergies can also be sent through CDX messenger as a content.

#### Attachments
Attachments including CDX documents and labs can be added to the document. 
{{% notice note %}}
Attachments and content can be sent together.
{{% /notice %}}

#### Save Draft
User at any time before sending the document can save the details of the documents as draft, which can be edited and sent later, as explained in the next section.  
{{< figure title="Draft saved" src="/images/savedDraft.png" width="70%">}}

#### Send
Once the data for the Cdx document has been entered completely, the document can be sent using the `Send` button at the bottom.

#### After Send
Sending the CDX document may take a few seconds. If the document was successfully sent to the CDX delivery system, the user will see a success window, which will close automatically after 10 seconds (see below).

{{< figure title="Success message" src="/images/successCdxComposer.png" width="110%">}}


{{% notice note %}}
**Successfully sending a document to the CDX system does *not* imply *instant* delivery at the target clinic(s).** The CDX messaging system will securely hold the sent document until it is downloaded by the recipient EMR system(s). Different EMRs may check the CDX system at different times for new documents to download. OSCAR users may check the delivery status of sent documents, as explained in the next section.
{{% /notice %}}

If there was an error sending the document using the CDX system, users will see a window with the error message (see below). The error message may help users or their OSPs to resolve the problem and try again.

{{< figure title="Sample error message" src="/images/errorCdxComposer.png" width="100%">}}
