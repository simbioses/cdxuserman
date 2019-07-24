---
title: "Demographic data consistency warning"
date: 2019-06-14T11:37:29-07:00
weight: 18
draft: true
---

Even if the CDX Import Engine was able to positively match the patient named in the document with a demographic record in the EMR (using 4 point matching, see above), the Import Engine may alert the user about inconsistencies between the demographic data in the document and in the EMR (see below).

![](https://paper-attachments.dropbox.com/s_D8F55B926E14BC491F2DAD18D930CB06AD57C72BB921C2ECDB6B0AA89F2D0027_1558125081142_image.png)


The clinician may use this warning as a motivation to review and possibly update the patient demographic record.

Note:



{{% notice info %}}
<p>The update of demographic data is currently not automated.</p>
<p>There is currently no way to “dismiss” the warning. It will remain in the Inbox viewer even after the demographic record has been updated. (this will be improved in the next version)</p>
</p>
{{% /notice %}}
