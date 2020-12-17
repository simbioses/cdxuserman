
+++
title = "CDX Composer"
date =  2019-06-14T11:37:29-07:00
weight = 15
chapter = true
+++


# CDX Composer

The current OSCAR version supports bidirectional workflow for various documents types. As a primary care EMR, OSCAR currently supports the *sending* and the *receiving* of Information request, Progress note, and Patient summary (responses from specialists).  

{{<mermaid align="left">}}
graph LR;
    A(CDX Composer .)  -->|information request, progress note, patient summary| B(Specialist EMR .)
{{< /mermaid >}}

CDX Composer can be opened by clicking "CDX Composer" on the top right of the main window as shown in the pic below.
{{< figure title="To open CDX Composer" src="/images/reachCdxComposer.png" width="100%">}}
