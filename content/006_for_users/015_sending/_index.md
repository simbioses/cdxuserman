
+++
title = "Bidirectional Workflows: e-Referrals"
date =  2019-06-14T11:37:29-07:00
weight = 15
chapter = true
+++


# Bidirectional Workflows: e-Referrals

The current OSCAR version supports only one kind of bidirectional workflow, namely *e-Referrals*. As a primary care EMR, OSCAR currently supports the *sending* of e-referrals and the *receiving* of consult notes (responses from specialists).  

{{<mermaid align="left">}}
graph LR;
    A(OSCAR .) -->|e-referral .| B(Specialist EMR .)
    B -->|consult note .| A
{{< /mermaid >}}

{{% notice note %}}
The term *referral* is used to align with provincial standard terminology. OSCAR has traditionally used the term *consultation*. They can be considered synonymous in the following.
{{% /notice %}}
