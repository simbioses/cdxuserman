---
title: "Referral"
date: 2019-09-09T17:24:59-07:00
weight: 10
---

The creation of e-Referrals (called "consultations" in OSCAR) is supported by the Java Server Pages (JSP) page `ConsulationFormRequest.JSP`. Received CDX documents are shown as possible attachments using the `showCdxDocumentArchive` JSP.

The corresponding actions for sending CDX documents are implemented in `EctConsultationRequestFormAction.java`. Sent referral documents and received responses can be shown using `showCdxDocumentArchive` JSP.

```mermaid
classDiagram
  ConsultationFormRequest --> EctConsultationRequestFormAction : struts-config.example
  ConsultationFormRequest : <<JSP>>
  ConsultationFormRequest --> showCdxDocumentArchive : <<link>>
  ConsultationFormRequest --> attachConsultation2 : <<link>>
  attachConsultation2 --> showCdxDocumentArchive : <<link>>
  EctConsultationRequestFormAction --> ISubmitDoc
  EctConsultationRequestFormAction : doCdxSend()
  ISubmitDoc : newDoc()
  ISubmitDoc : updateDoc()

```

CDX documents and their attachments can be attached to consultation requests. (`attachConsultation2` has been extended to allow this.)
