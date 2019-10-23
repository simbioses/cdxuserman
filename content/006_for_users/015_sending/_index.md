
+++
title = "Bidirectional Workflows: e-Referrals"
date =  2019-06-14T11:37:29-07:00
weight = 15
chapter = true
+++


# Bidirectional Workflows: e-Referrals

The current OSCAR version supports only one kind of bidirectional workflow, namely *e-Referrals*. Other types of workflows may be supported in the future.

{{% notice note %}}
The term *referral* is used to align with provincial standard terminology. OSCAR has traditionally used the term *consultation*. They can be considered synonymous in the following.
{{% /notice %}}

{{<mermaid align="left">}}
graph LR;
    A(OSCAR .) -->|e-referral .| B(Specialist EMR .)
    B -->|consult note .| A
{{< /mermaid >}}
