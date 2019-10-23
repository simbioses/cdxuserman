---
title: "Sending an e-Referral"
date: 2019-06-14T11:37:29-07:00
weight: 6
---

E-referrals are created in the usual way (like normal “consultations”) . Of course, it is important to select a CDX-capable specialist as the “Consultant”. These specialists are clearly marked in the dropdown box with a “(CDX)” prefix to their names (see below). They are also at the top of the list of specialists.

{{% notice info %}}
Some providers may work for more than one clinic. In this case, the user can select a specific clinic to direct the referral to. Alternatively, the referral can be sent to all clinics the provider works for.
{{% /notice %}}

{{< figure title="Select a CDX capable recipient" src="/images/consultform.png" width="100%">}}


Attachments can be added to the e-Referral (see left side of window). Once the data on the referral has been entered completely, the e-referral can be sent using the `Submit and Save CDX e-Referral` button (top right or buttom right).

Sending the e-Referral may take a few seconds. If the document was successfully sent to the CDX delivery system, the user will see a success window, which will close automatically after 5 seconds (see below).

{{< figure title="Success message" src="/images/send_success.png" width="100%">}}

{{% notice note %}}
**Successfully sending a referral document to the CDX system does *not* imply *instant* delivery at the target clinic(s).** The CDX messaging system will securely hold the sent document until it is downloaded by the recipient EMR system(s). Different EMRs may check the CDX system at different times for new documents to download. OSCAR uses may check the delivery status of sent documents, as explained in the next section.
{{% /notice %}}

If there was an error sending the e-referral using the CDX system, users will see a window with the error message (see below). The error message may help users or their OSPs to resolve the problem and try again. Alternatively, users may choose to send the referral using conventional means (e.g., fax).

{{< figure title="Sample error message" src="/images/send_error.png" width="100%">}}
