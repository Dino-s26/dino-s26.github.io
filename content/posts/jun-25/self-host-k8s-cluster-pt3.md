---
ShowBreadCrumbs: true
searchHidden: false
author: ["Dino"]
title: "Self-host Kubernetes Cluster Pt.3"
date: 2025-06-10
tags: 
- kubernetes
- k8s
- microk8s
- self-host
- tech
- learning
---

## Intermezzo

![Intermezzo](/img/jun-25/self-host-kubernetes-pt3/self-host-kubernetes-pt3.png)

<p style='text-align: justify;'>

Howdy :cowboy_hat_face:, and welcome back to third part of self-host kubernetes !

This part we will create simple K8s cluster with Minikube and how we will setup
in MacOS (for linux and windows should be similar, but you will need to adjust a bit).

Before we start we will need to prepare couple of stuff as follows

- Minikube (v1.35.0)
- Docker Desktop (4.40.0)

</p>

## Start single node K8s

<p style='text-align: justify;'>

Let's start with simple task here, creating single node K8s with Minikube.
This Node will act as Control-Plane and Worker.

Assumming you already install the required software, before we begin,
let's check if you ready with the requirement:

run below command to get the minikube version

```
minikube version
```

it should output something similar as follows:

![Minikube version](/img/jun-25/self-host-kubernetes-pt3/minikube-version.png)

next for the Docker run this command

```
docker version
```

it should output something similar as follows:

![Docker version](/img/jun-25/self-host-kubernetes-pt3/docker-version.png)

So now we already confirm we have all the tools we need, here comes the stuff you've been waiting for ...

1. To start new Minikube node, you can run this command

```
minikube start
```

it should output something similar as follows:

![Minikube Start](/img/jun-25/self-host-kubernetes-pt3/minikube-start.png)

now, as the command finished, we can verify with this command and have the followings output:

```
minikube status
minikube node list
```

![Minikube Status](/img/jun-25/self-host-kubernetes-pt3/minikube-status.png)

![Minikube Node list](/img/jun-25/self-host-kubernetes-pt3/minikube-node-list.png)

</p>

## Access Minikube K8s node

So now we have create our first K8s node with minikube, but how do we access it ?

that's actually good question ...
usually we use kubectl, but minikube already have built-in command for it so we don't have to install kubectl,
but if you want to install kubectl you can also do it.

so here are the way we can interact with Minikube K8s Node.

![Minikube kubectl](/img/jun-25/self-host-kubernetes-pt3/minikube-kubectl.png)
