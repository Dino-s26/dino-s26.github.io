---
ShowBreadCrumbs: true
searchHidden: false
author: ["Dino"]
title: "Self-host Kubernetes Cluster Pt.2"
date: 2025-05-12
tags: 
- kubernetes
- k8s
- microk8s
- self-host
- tech
- learning
---

## Intermezzo

![Intermezzo](/img/may-25/self-host-kubernetes-pt2/self-host-kubernetes-pt2.png)

<p style='text-align: justify;'>

Howdy :cowboy_hat_face:, and welcome back to second part of self-host kubernetes !

Before jump to the cluster setup and all the technical thingy, it's better to understand
the base components of how Kubernetes work and how it will interract
each other within the cluster or just single instance of Kubernetes

I will try to make it compact and informative for everyone with little knowledge of Kubernetes
understand how it work and help them to catch up quickly with some of terminology that I will use
within my posts (there will be lot's of part to deep dive into Kubernetes)

</p>

## Virtualization vs Containerization

![Historycal Context Kubernetes](/img/may-25/self-host-kubernetes-pt2/container-evolution.png)

In Traditional Deployment term, what we know when setup application is that, we will have followings:

- Hardware (1 hardware can host multiple application or multiple hardware can host multiple application )
- OS (it can be vary and become problem in the future)
- APP with multiple dependencies (each App will have different dependencies and OS and/or Hardware compatibility)

Within Virtualization, what differentiate it from Traditional way of deploying application as followings:

- VM's (Virtual Machine) will be host under hypervisor which can run virtualization, it can be same it can be different hypervisor
- VM's still require OS and dependencies to run, and Application will run within the VM not the hypervisor
- Virtualization utilize scalability of physical hardware to isolate each VM's while also utilize the Hardware, which shared accross VM's

what about Container ?

In container, the only different to virtualization as follows:

- Container remove Hypervisor Layer and directly interact with the Host OS, let's say you install docker on linux it will automatically use linux container runtime, in windows it is kind of different because you're using windows runtime to run linux container but it is also possible to run windows container on it, I will not deep dive this as this is out of the scope for now
- Container not really require OS to host the APP, but it will interact with the app image that are compiled under the container runtime we use (CMIIW, it's been while I learn container terminology so pardon my explanation here)
- Lastly Container will still need dependencies but it will tailor to the image we use

[Summary Reference source](https://kubernetes.io/docs/concepts/overview/)

## The Main Menu ... Kubernetes Core Component

![Kubernetes Component](/img/may-25/self-host-kubernetes-pt2/kubernetes-component.png "Kubernetes Component")

<p style='text-align: justify;'>

Let's jump into the Core Component of the Kubernetes. As we self-hsot our Kubernetes Cluster the main component we need to aware are **"Control Plane/s"** and **"Node/s"**

</p>
