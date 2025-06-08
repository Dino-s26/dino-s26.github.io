---
ShowBreadCrumbs: true
searchHidden: false
draft: true
author: ["Dino"]
title: "[DRAFT] How much cost did I save by moving to AWS S3 ? Pt.3"
date: 2024-11-10
tags: 
- aws
- s3
- gcp
- cloud storage
- cloud tech
- tech
---


## Intermezzo

![Intermezzo](/img/nov-24/moving-data-gcp-to-aws-s3-pt3/mr-krab-money.png)

<p style='text-align: justify;'>
Henlo again ...
been while not updating, yeah been busy with lot of stuff especially work and also with moving to new places ...

In this part I will update regarding the Cost I spend for store most of my data in AWS S3 since I move 90% of my backup from GCP Cloud Storage :sweat: ...
</p>

## Disclaimer

<p style='text-align: justify;'>

Let me put **"Disclaimer"** first, since this is still my ongoing project to deprecate GCP Cloud Storage, the cost here is what I can present based on the data I gather and process on [Vantage](<https://vantage.sh>) that have been integrated to get detail costs from both Cloud Platform.
</p>

## Here's what cost me during my migration from GCP to AWS

So monthly I usually spend around **~150k IDR** to **~350k IDR** for GCP Cloud Storage cost. So This cost actually is based on Storage Class and operation I do within the GCP Cloud Storage, you can see the breakdown as follows:

**[GCP Storage Class Breakdown]**![GCP Class Cost Breakdown](/img/nov-24/moving-data-gcp-to-aws-s3-pt3/gcp-cloud-storage-cost-class-breakdown.png "GCP Storage Class Breakdown")

As a note, the standard class cost increase do to my migration data. TL;DR, my data are inside **Archive Class** and **Nearline Class**, which I need to change the class to **Standard Class** inorder to move the data without adding additional cost, also the data was stored in Singapore Region :cry:.

*PS: Ignore November 2024 here as the data not yet consolidated*

**[GCP Cost Billing]**

Moving on ...
here's the total billing each month I pay (Converted to $, for AWS Cost Comparison) ...
![GCP Cost Billing](/img/nov-24/moving-data-gcp-to-aws-s3-pt3/gcp-cloud-storage-cost.png "GCP Cost Billing")

As we can see this is all the cost I have to pay during my data migration from GCP Cloud Storage to AWS S3 ...

The spike in price is expected as we change the storage class and download data during the migration.
