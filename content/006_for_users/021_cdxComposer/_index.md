
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
    A(CDX Composer .) -->|information request, progress note, patient summary| B(Specialist EMR .)
{{< /mermaid >}}

{{% notice note %}}
CDX Composer uses traditional inbox to receive information request, progress note and patient summary.
{{% /notice %}}