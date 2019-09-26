---
title: "Recipient Registry"
date: 2019-06-14T11:37:29-07:00
weight: 3
---

The CDX infrastructure has a **provider registry**, which contains address information about all CDX-capable providers and clinics.

OSCAR has traditionally kept its own database of specialists. The CDX extension of OSCAR allows users to query the CDX provider registry and add/edit CDX capable providers in OSCAR's database.

### Adding new CDX-capable specialists
The “Add Specialist” UI has been extended to add CDX-capable specialists (click the `Add CDX-Enabled Specialist` link at the top).


{{< figure title="Extended UI for adding specialists" src="https://paper-attachments.dropbox.com/s_D8F55B926E14BC491F2DAD18D930CB06AD57C72BB921C2ECDB6B0AA89F2D0027_1558132363494_image.png" width="100%">}}

The new dialogue allows the user to search the CDX provider registry for providers to add to OSCAR. The search is based on any letters and returns all providers with these letters in their names.

{{< figure title="CDX Provider Registry Search" src="/images/search_spec.png" width="100%">}}

The search should return a list of specialists that meet the search criteria. They can be added using the `add` button.

![](https://paper-attachments.dropbox.com/s_D8F55B926E14BC491F2DAD18D930CB06AD57C72BB921C2ECDB6B0AA89F2D0027_1558132543256_image.png)

⚠️ At this point, Interior Health Authority's provider registry does not provide us with the speciality of providers. Therefore, the speciality needs to be added manually in the usual way:

> Show All Services → Select a service, e.g., Cardiology → Select the specialists that provide this service → Click Update


![](https://paper-attachments.dropbox.com/s_D8F55B926E14BC491F2DAD18D930CB06AD57C72BB921C2ECDB6B0AA89F2D0027_1558132847335_image.png)

### Updating existing specialists

Clinics will likely have existing specialists in their EMR database. OSCAR allows users to update these records where existing specialists are found to be CDX capable. This can be done automatically or manually. The `Edit Specialists` UI has a new `Test CDX Availability` function that (if invoked) will test the CDX availability of *all* specialists stored at the clinic (and update the local database accordingly).

{{< figure title="Updating existing specialists (automatic, batch)" src="/images/spec_update.png" width="100%">}}

Existing specialist records can also be updated manually to add their CDX credentials. For that purpose, select a specialist record and add the CDX id for this provider.

{{< figure title="Updating existing specialists (manually, single specialist)" src="/images/spec_update_man.png" width="100%">}}
