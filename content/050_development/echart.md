---
title: "eChart"
date: 2019-09-09T16:55:18-07:00
weight: 9
---

CDX documents appear in the patient encounter screen (eChart) under "Documents". The code to display documents has been modified to use the `showCdxDocumentArchive` page if the document is from CDX.

```mermaid
classDiagram
  EctDisplayDocsAction --> EDoc : isCDX()
  EctDisplayDocsAction --> showCdxDocumentArchive : <<link>>
```
