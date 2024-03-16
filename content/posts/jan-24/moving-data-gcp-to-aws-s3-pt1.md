---
ShowBreadCrumbs: true
searchHidden: false
author: ["Dino"]
title: "Why I'm moving to AWS S3 Pt.1"
date: 2024-01-10
tags: 
- aws
- s3
- gcp
- cloud storage
- cloud tech
- tech
---

## Intermezzo

<p style='text-align: justify;'>

Henlo and Happy New Year 2024, I hope you guys in healthy condition and still have lots to achieve this year :grin:

So, actually long story short ...
I'm actually GCP person since 2018/2019, the first time I ever learn Cloud Tech and also getting my first certificate
in cloud, even tho it is still associate level lol. By that time I'm getting used to using GCP as my primary Cloud Stacks

Until recently I learn AWS Cloud Stack Tech, which way more simplified and also integration with their CLI tools more seamless, 
GCP CLI is not that bad, I just rarely use it, unlike AWS CLI since my work related to AWS Stacks

</p>

## The 'Why moving to AWS S3' 

even tho I realize that my bill for GCP Cloud Storage are around `~$50/month` with Indonesia Tax around 11% (used to 10%, 
since the changes with Indonesia Tax Rules in the past), this is way cheaper tbh for me, but there are couple of stuff makes me realize that I want to move to AWS S3 for some reasons (Which in this matter are subjective to my own oppinion):

1. AWS S3 can provide me with granular dashboard where I can see how much data I have, for comparison here's how the dashboard looks

##### GCP Cloud Storage Dashboard
![Image-1](/img/jan-24/moving-data-gcp-to-aws-s3-pt1/image-1.png)

<p style='text-align: justify;'>

> **Side notes**: even tho we can customize the dashboard, it doesn't have build in dashboard to see the data we look for (e.g. Storage/
objects sizes), which become problematic and somehow not really in favor for me to check how much Data I have in my Cloud Storage

</p>
{{< line_break >}}

##### AWS S3 Dashboard
![Image-2](/img/jan-24/moving-data-gcp-to-aws-s3-pt1/image-2.png)

{{< line_break >}}

2. S3 provide more robust API (CRR, Object Tagging, )

<p style='text-align: justify;'>

Even to I not using CRR (Cross Region Replication) for now, but I'm planned to use it in the future.
So if in the future I decide to moving somewhere I could just replicate the data to the nearest region to access my data.

Other than that, I usually use Cyberduck to access data, so in the event I need to access some data (e.g. download / upload) I could 
just do it seamlessly

</p>
{{< line_break >}}

3. Granular IAM to the level of it is objects

<p style='text-align: justify;'>

To be fair, It is really tricky to share files in Cloud Storage. Not saying it is hard but it needs extra steps in order to share files, especially if the files itself is in bigger sizes like GBs of files, also we need to do it via gcloud / gsutil in order to create the [signed-URL](https://cloud.google.com/storage/docs/access-control/signing-urls-with-helpers) in GCP Cloud Storage.

Compare with S3 we can just create the signed-URL directly from [AWS Console](https://docs.aws.amazon.com/AmazonS3/latest/userguide/ShareObjectPreSignedURL.html), which I realize it would be easier for me when I decide to transfer knowledge in form of recording files
that are big and need times to download :laughing:.

</p>

## The Pros and Cons of AWS S3

<p style='text-align: justify;'>

Honestly, moving from GCP Cloud Storage to AWS S3 give me some impression of pros and cons I will need to deal with the rest of my 
life time using the services ...

**Pros** :

- More Mature Product (personally)
- More secure with IAM control level, even to the object itself
- Work flawlessly with aws-cli, as long as you have the right permissions, which kind of help when I want to access the data from EC2 instances directly
- More advance and have it is case by case, even migrating data from 2 different AWS account can be done easily
- More cheaper per GB pricing the first 50 TB Data, which around $ ~0.0025/GB, compared GCP Cloud Storage around $ ~0.020/GB, the detail can be checked [here](https://aws.amazon.com/s3/pricing/)
- [There will be no Data Transfer charge if you transfer between EC2 or other AWS Services](https://arc.net/l/quote/vrotbjcb)
- You can export list of the data from AWS S3 Bucket into inventory reports, with a bit configuration [here](https://docs.aws.amazon.com/AmazonS3/latest/userguide/storage-inventory.html)


**Cons** :

- "**There are per-request ingest charges when using PUT, COPY, or lifecycle rules to move data into any S3 storage class**", this is from official [AWS S3 Page](https://arc.net/l/quote/lipqpept), which kind of bummer for me, compared to GCP Cloud Storage which still free for standard class storage :smiley:
- By defult AWS S3 access are private, be sure to change it accordingly whenever convenient
- It is bit advance at first so take your time to adjust and learn it, dont worry you wont be charge until you put data on it (cmiiw on this)
- AWS Free Tier with 5GB only the first 12 Month you use AWS S3, after that you'll be charge

</p>

#### End of Part-1

Since it is to long to put into 1 post, I will break it down into couple of parts

- [Part-2, How I'm moving to AWS S3 Pt.2]({{< ref "/moving-data-gpc-to-aws-s3-pt2/" >}} "Moving Data from GCP to AWS S3 Part-2")
- [TBD Part-3]({{< ref "" >}} "")
