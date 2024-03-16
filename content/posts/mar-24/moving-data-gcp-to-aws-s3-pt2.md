---
ShowBreadCrumbs: true
searchHidden: false
author: ["Dino"]
title: "How I'm moving to AWS S3 Pt.2"
date: 2024-03-16
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

Henlo, back again ...
Been a while I haven't write the continuation of this story of how I'm moving my data from GCP Cloud Storage to AWS S3.
much already happen, this and that don't really want to go into detail but here I'm back ...

but before begin, sorry for not posting but I will try to make post about anything for now atleast once a month, if I dont have
then I don't really have something to post at the moment and probably busy with my new job :sweat:. 

</p>

## The Main Experiences Stuff ...

<p style='text-align: justify;'>
Before I break it down, as a note this part only explain on the experience and actual data I do during the migration.
Any 
Without further chity chaty, here's what you need to prepare before moving forward:

### Things to Prepare:

1. New EC2 Instance, I Suggest have it pair with 500GB EBS (as minimum, depend on how much data you will migrate) in a different partition from the root partition.
2. New S3 Bucket.
3. AWS CLI and GCP SDK installed on New EC2 instance
4. New IAM Role for New EC2 Instance to access new S3 Bucket
5. Make sure the GCP Cloud Storage are in "Standard Class" or else it will incur really expensive price when migrated.

### Illustration on what it should look like ...

Here's the setup I do to migrate the data from GCP Cloud Storage to AWS S3

![Illustration](/img/mar-24/moving-data-gcp-to-aws-s3-pt2/illustration-on-gcp-to-aws-migration.draw.io.png)

and here's some quick explanation on the flow of what I do here:

1. Created new EC2 instance which have 32GB Root Partition and 500GB EBS Storage mounted as separated partition for temporarily store my data from GCP Cloud Storage.
2. Install AWS CLI and GCP SDK on the EC2 Instance and configure it properly.
3. Download data from GCP Cloud Storage to the EBS Partition, this process takes time and several batch.
4. Once data is ready, Upload to the AWS S3 Bucket in batch, this also takes time to process.
5. Repeat steps 3 - 4, until all the data or partial of the data is migrated.
6. Done

Pretty straight forward isn't ?, yes and no actually :sweat_smile: ...
Let's Deep dive a bit ...
</p>

### A little bit deep dive

<p style='text-align: justify;'>

Previously I said it is straight forward process, but there is catches here which I'm willing to explain a bit why 

1. Migrating the data from GCP Cloud Storage to AWS S3 are a bit costly, as you can see from the capture below *(the 3 spike bars)* this is how much it cost for me to migrate *(refer to the total cost)*
data from GCP Cloud Storage to AWS S3 from December 2023 to January 2024 :smiling_face_with_tear:, converted to USD + Taxes are around $106.
{{< line_break >}}{{< line_break >}}
Actually not that much, but if it is for personal usage it really cost you fortune, especially if you are conservative with your own budget.
The reason why it is costly because at the time the data on GCP Cloud Storage are stored as Archival Storage Class Type, and I forgot to convert the objects into Standard Class Type Storage for the objects that are stored more than 1 year in the Cloud Storage.

![First Batch Migration Cost](/img/mar-24/moving-data-gcp-to-aws-s3-pt2/gcp-cloud-storage-cost.png "Cloud Storage Cost from Dec-23 to Jan-24")

2. Transfering around **~4459** Objects are no easy job :sweat_smile:, it need to be done in batches *(Spoiler: I use multiple tmux session to batch the process)*. Capture shown below are majority larger Object Data I've already migrated to AWS S3 so far, as it related to point number 1 above I still have data left to be migrated to AWS S3, but I still haven't have time to continue the work.

![Data Count on GCP Cloud Storage Migrated so far](/img/mar-24/moving-data-gcp-to-aws-s3-pt2/total-object-gcp-cloud-storage-on-aws-s3.png "Total Data Migrated so far")

3. Cost wise, it is not far from GCP Cloud Storage. I'm not sure why it is bit expensive on AWS S3 at this moment, but compared the usage from **Sep-23 to Nov-23** on GCP Cloud Storage the cost spend on GCP are less than what already spend on AWS S3 on Jan-24 to Mar-24 (Prorated).
{{< line_break >}}
*Note: Using this range of date as it is more accurate data for non-operational cloud storage operation*

[AWS S3 Jan-24 to Mar-24 (Prorated)]
![AWS S3 Cost](/img/mar-24/moving-data-gcp-to-aws-s3-pt2/s3-cost.png "Total Cost After Migrated to AWS S3")
[GCP Cloud Storage Sep-23 to Nov-23 for comparison]
![GCP Cloud Storage Sep-23 to Nov-23](/img/mar-24/moving-data-gcp-to-aws-s3-pt2/gcp-cloud-storage-sep-23-to-nov-23.png "Cost from Sep-23 to Nov-23 for comparision")

</p>

## End of Part-2

That's a little bit of deep dive of what happen during the migration of the data from GCP Cloud Storage to AWS S3
will continue in the next part for the technical stuff

- [Part-1, Why I'm moving to AWS S3 Pt.1](/posts/jan-24/moving-data-gcp-to-aws-s3-pt1/ "Moving Data from GCP to AWS S3 Part-1")
- [TBD Part-3]({{< ref "" >}} "")
