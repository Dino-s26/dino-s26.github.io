---
ShowBreadCrumbs: true
searchHidden: false
author: ["Dino"]
title: "Self-host Kubernetes Cluster Pt.2"
date: 2025-05-22
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

I'll try to keep it compact and informative, so that people with little knowledge of Kubernetes can understand how it works
and help them to catch up quickly with some of terminology that I will use
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

Let's jump into the Core Component of the Kubernetes. As we self-host our Kubernetes Cluster the main component we need to aware are **"Control Plane/s"** and **"Node/s"**

### Control Plane

Basically Control Plane are the brain of K8s cluster, where it manage all the communication of it is Data Plane (Node/s), there are several compoenent within Control Plane we need to understand how it work, followings are the Control Plane components:

![Control Plane Component](/img/may-25/self-host-kubernetes-pt2/control-plane-components.png "Control Plane Component")

- **kube-apiserver**

Kube API Server are the core component that expose Kubernetes HTTP API to be able to communicate outside of K8s Cluster.
This core component will handle all the request such as `kubectl` command and the communication of the request coming into K8s cluster.

- **etcd**

ETCD are persistent database that keeping track of all the changes happen inside the K8s cluster.
Usually in big cluster it will use ETCD, but on smaller deployment can use sqlite or dqlite depending on the needs and requirement.

- **kube-scheduler**

Kube Scheduler are the component that communicate between Kube API Server and the Node/s, in this case the scheduler will oversees
the spawn of Pod/s that will be deployed to the Node/s, this also will taken some factors such as: individual and collective resource requirements, hardware/software/policy constraints, affinity and anti-affinity specifications, data locality, inter-workload interference, and deadlines.

- **kube-control-manager**

Lastly Kube Control Manager that will oversees the high level of K8s cluster, which mean the overall process happen inside the control plane.

For example:

- Node Controller, this will oversees the Node/s scale-up or scale-down and also keep track of the keep Alive of the nodes

- Job Controller, this will oversees the CronJob that happen within the K8s cluster, which also track the success and fail state of the job

above are just example, but only giving how the Kube control Manager work in general

### Node (Data Plane)

![Node Component](/img/may-25/self-host-kubernetes-pt2/node-components.png "Node Component")

Node or we can just straightly say Data Plane, are the part where kubernetes deploy it is services like pods, ingress, persistent-volume, etc.
There are also couple component within the Node (Data Plane) such as:

- **Kubelet**

Kubelet are basically the component where it communicate with the node and also the Kubernetes control-plane API, which ensure the Pods
and it is container or side-car container run properly within the node.

- **Kube-Proxy**

Kube-Proxy are basically the component where it maintain network rules and communication within or inter-node communication.
This mean only including L4 (TCP/UDP) communication is use within the node (intern-node) and inside the node such as network routing for services (Node IP, Cluster IP, etc), but excluding the pod-to-pod DNS communication, only service-to-pod communication (please cmiiw on this, still kind of lost sometimes).

Since we use CNI to handle pod-to-pod IP communication which will communicate with the Kube-Proxy or other available network plugin.

for easier to understand terminology can refer to this table below

|Layer|Handle By|Role as|
|:----|:-------|:------|
|L2/L3|CNI     |Pod-to-pod networking|
|L4   |kube-proxy|Service-level load balancing|
|L7   |Ingress/Mesh|App-aware routing, TLS, etc|

- **Container Runtime**

Container runtime are the underlay which pods / container within the node will be use to run the containers effectively and handle it is lifecycle and execution of the containers.

Within kubernetes, it will use container runtime like Docker, ContainerD, CRI-O, and other that supported by kubernetes.

</p>

## What to see on next part ?

I hope my Part-2 writing series of Self-host Kubernetes can help you (and also myself) learn more about kubernetes deeper.
Next part I will post about how to host it in your local machine and what are the things you need to prepare in-order to host it properly.

Dont worry I will not host it on AWS or other Cloud Platform :wink:, and see you on my next post.
