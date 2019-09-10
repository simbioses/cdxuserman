---
title: "Import"
date: 2019-09-09T10:27:36-07:00
weight: 5
---

The automatic import of CDX documents is handled by an 'OSCAR job' (see class `CDXDownloadJob`). The download job is configured using class `CDXConfiguration`. The UI for the CDX configuration is implemented as a Java Server Page `cdxAdmin`, which invokes a Struts-action `CDXAdminAction`.

The actual import algorithm is implemented in class `CDXImport`. CDXDownloadJob calls its `importNewDocs` method.

```mermaid
classDiagram
  OscarRunnable <|-- CDXDownloadJob
  CDXConfiguration --> CDXDownloadJob : <<create>>
  CDXConfiguration : savePolling()
  CDXAdminAction --> CDXConfiguration : savePolling(enabled, interval)
  CDXAdminAction : execute()
  cdxAdmin --> CDXAdminAction : struts-config.xml
  cdxAdmin : <<JSP>>
  CDXDownloadJob : run()
  CDXDownloadJob --> CDXImport : importNewDocs()
  CDXImport : importNewDocs()
  CDXImport : importDocs()
  cdxImportNewDocs --> CDXImport : importNewDocs()
  cdxImportNewDocs : <<JSP>>
  cdxAdmin --> cdxImportNewDocs : <link>
  cdxAdmin --> cdxImportOldDocs : <link>
  cdxImportOldDocs --> CDXImport : importDocs(id-list)
```

In addition to automatic download, the users can manually trigger the import of new CDX documents. This is implemented with a separate button that loads another JSP (`cdxImportNewDocs`), which invokes the import function.

Finally, the UI also supports manual download of *selected* messages from the CDX infrastructure. This is implemented with a separate button that loads a third JSP (`cdxImportOldDocs`), which invokes a second  import function that takes a list of message IDs (`importDocs`).

```mermaid
sequenceDiagram
  CDXDownloadJob ->> CDXImport : importNewDocs()
  CDXImport ->> obibcon.ReceiveDoc : pollNewDocIDs()
  obibcon.ReceiveDoc -->> CDXImport : msg_ids

  CDXImport ->> CDXImport : importDocuments(msg_ids)

  loop for  each id : msg_ids
    CDXImport ->> obibcon.ReceiveDoc : retrieveDocument(id)
    obibcon.ReceiveDoc -->> obibcon.IDocument : new()
    obibcon.ReceiveDoc -->> CDXImport : doc
    CDXImport ->> CDXImport : storeDocument(doc, id)
    CDXImport -->> CdxProvenance : new()
    CDXImport ->> CdxProvenance : populate(doc)

    alt previous version
      CDXImport ->> CDXImport : reviseInboxData(doc)
    else no previous version
      CDXImport ->> CDXImport : createInboxData(doc)
    end

    Note over CDXImport, Document: If a new version of a document that is already in the inbox is received, the inbox item is revised.

    CDXImport -->> Document : new()

  end
```
