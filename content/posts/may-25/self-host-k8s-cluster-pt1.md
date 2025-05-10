---
ShowBreadCrumbs: true
searchHidden: false
author: ["Dino"]
title: "Self-host Kubernetes Cluster Pt.1"
date: 2025-05-02
tags: 
- kubernetes
- k8s
- microk8s
- self-host
- tech
- learning
---

## Intermezzo

![Intermezzo](/img/may-25/self-host-kubernetes-pt1/self-host-kubernetes-pt1.png)

<p style='text-align: justify;'>

Howdy :cowboy_hat_face:
Been while I didn't update my post here, so in this post I will update my journey on self-host my kubernetes cluster.
Yes you heard it right, since I got exposure to Kubernetes (also known as K8s in short) from EKS (Elastic Kubernetes Service by AWS) from my previous work. 
I got weird idea to fullfill my hands-on experience to learn more on K8s, also preparing myself if I change my mind in the future to get CKA certification.
</p>

## Preface

<p style='text-align: justify;'>

Before jump right at the topic, let's familiarize with K8s also known as Kubernetes.

if you new to the K8s or Kubernetes, here are the short summary of what is it ...

*Quoted from: Google and also [Kubernetes](https://kubernetes.io/) web*

```
Kubernetes also known as (K8s in short) are open-source 
container orchestration system to automate the deployment of application, 
scaling and also it is management.

Originally design by Google employee then continously maintained 
by CNCF (Cloud Native Computing Foundation) under Linux Foundation.
```

</p>

## Then what's make it different from Docker Swarm ?

<p style='text-align: justify;'>

Good question actually, the simple answer to it is how big is your application.
Docker Swarm are meant to be simple, small, lightweight and easy to setup at your disposal,
but it depend on Docker API container runtime

Meanwhile for Kubernetes, if you have multiple micro-service architechture which need to be meshed with other
services, Lage scale, have capability of self-healing and also support multiple container runtime API (not just Docker API,
can be containerd, CRI-O, etc), then kubernetes is your choices for this.

But to be fair, imo using kubernetes not always the best solution for your application :smile:, so back again to the needs.
</p>

## Why self-host my own kubernetes cluster ?

<p style='text-align: justify;'>

Back to the main question, as we already get the bare minimum of knowledge of Kubernetes ...

**WHY ?**

The simple answer I can come up with it are:

- I'm kind of person who have high curiosity of tech
- Familiarize myself with kubernetes tech stack
- Preparing CKA Exam in the future
- Have lab to do experimental and also danger things that can't be done on manage service kubernetes (I really don't want to mess up and get in trouble for it :wink: on company property haha)

</p>

## Pre-look of the setup I create

<p style='text-align: justify;'>

As this post already get too long, this will be end of Part-1, I will talk the technical stuff on next part of my post.

Before I close this, here's are the look of my Kubernetes Cluster Setup to you can have the idea what it's look like in logical term.

</p>

![Microk8s Cluster setup](/img/may-25/self-host-kubernetes-pt1/microk8s-diagram.svg "Microk8s Cluster Diagram")



- [Self-host Kubernetes Cluster Pt.2](/posts/may-25/self-host-k8s-cluster-pt1/ "Self-host Kubernetes Cluster Pt.1")