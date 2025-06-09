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
minikube profile list
```

![Minikube Status](/img/jun-25/self-host-kubernetes-pt3/minikube-status.png)

![Minikube Node list](/img/jun-25/self-host-kubernetes-pt3/minikube-node-list.png)

![Minikube Profile list](/img/jun-25/self-host-kubernetes-pt3/minikube-profile-list.png)

</p>

## Access Minikube K8s node

<p style='text-align: justify;'>

So now we have create our first K8s node with minikube, but how do we access it ?

that's actually good question ...
usually we use kubectl, but minikube already have built-in command for it so we don't have to install kubectl,
but if you want to install kubectl you can also do it.

so here are the way we can interact with Minikube K8s Node.

![Minikube kubectl](/img/jun-25/self-host-kubernetes-pt3/minikube-kubectl.png)

so now, let's check if our node is control-plane, using following command:

```
minikube kubectl -- get nodes
```

it should output as follows:

![Minikube kubectl -- get nodes](/img/jun-25/self-host-kubernetes-pt3/minikube-get-nodes.png)

you must be asking, why I use 2 different command here, "minikube kubectl -- get nodes" and
"minikube kubectl -- get nodes -o wide", right ? ... so in here we can see the detail when using "-o wide".

Now we can observe that the ***Container Runtime*** using Docker with engine version 27.4.1.

But what if we use other engine version other than docker ?, we can refer to my existing cluster I have here as comparison

![microk8s get nodes](/img/jun-25/self-host-kubernetes-pt3/mk8s-get-nodes.png)

as we can see the ***Container Runtime*** I use on my production cluster is using **Containerd** with engine version 1.7.27.

</p>

## Adding additional Node as Worker

<p style='text-align: justify;'>

So now we have 1 Node as Control-Plane, what if we want to add another Node but as worker ?
Good question, let's jump to practice ...

In order to add additional Nodes, we can use followings command:

![minikube node add](/img/jun-25/self-host-kubernetes-pt3/minikube-node-add.png)

```
minikube node add --worker=true
```

now it should output something like this:

![minikube node add output](/img/jun-25/self-host-kubernetes-pt3/minikube-node-add-output.png)

okay so now is should be deployed, but there is actually error as we dont have CNI configured during
cluster initiation, this is normal and we will come back to this later once we check the Nodes.

Now let's check our current nodes

![minikube node list](/img/jun-25/self-host-kubernetes-pt3/minikube-node-add-worker.png)

![minikube node profile list](/img/jun-25/self-host-kubernetes-pt3/minikube-profile-list-worker.png)

![minikube node kubectl get nodes](/img/jun-25/self-host-kubernetes-pt3/minikube-kubectl-get-nodes-add-worker.png)

Now you can see that our node worker added to the cluster, but the **Roles** is empty right?
This is normal as we haven't assign **Label** to the Nodes we create, in order to fix it we can fix it by run this command

```
minikube kubectl -- label node <node name> node-role.kubernetes.io/worker=true

for example: 
minikube kubectl -- label node minikube-m02 node-role.kubernetes.io/worker=true
```

If we check again on our node, it should be fixed by now ...

![minikube check node label](/img/jun-25/self-host-kubernetes-pt3/minikube-check-node-label.png)

</p>

## Fixing CNI Error

<p style='text-align: justify;'>

So as we add the second node, we see the CNI error, now we have to fix it, since we create 1 node at first and add another after cluster is created.

Now let's stop our current minikube cluster with following command:

```
minikube stop
```

it should stop our minikube cluster like this

![minikube stop](/img/jun-25/self-host-kubernetes-pt3/minikube-stop.png)

Once, stopped we will need to recreate the cluster *(actually it can also be done by adding '--cni' option when we start the cluster again, but this time we will create it properly with 1 worker added to the cluster as adding '--cni' option not spawning calico pods)*, before that we need to delete the current cluster, you can use following command:

```
minikube delete
```

![minikube delete](/img/jun-25/self-host-kubernetes-pt3/minikube-delete.png)

now let's run this commmand to recreate the minikube cluster with CNI and additional 1 node as worker:

```
minikube start --cni=calico -n 2
```

![minikube start cni](/img/jun-25/self-host-kubernetes-pt3/minikube-start-cni.png)

Once the cluster is started, dont forget to add the label for the worker like earlier and let's check the calico Pods with following command:

```
minikube kubectl -- get pods -A
```

it should output as follows:

![minikube check-pods](/img/jun-25/self-host-kubernetes-pt3/minikube-check-pods.png)

we see some error with out **"calico-kube-controller"** pod here, this is because the calico-kube-controller try to run first before the **"calico-node"** pods running,
as the result the **"calico-kube-controller"** give **"error"** state and it is normal. let's wait for all the **"calico-node"** pods running then we will restart the deployment
of **"calico-kube-controller"** with the following command:

```
minikube kubectl -- rollout restart -n kube-system deploy/calico-kube-controller
```

![minikube restart calico-kube-controller](/img/jun-25/self-host-kubernetes-pt3/minikube-restart-calico-kube-controller.png)

Next, we can check the pods status again and verify if the **"calico-kube-controller"** running well

![minikube check pods calico](/img/jun-25/self-host-kubernetes-pt3/minikube-calico-kube-controller-status.png)

Now let's check the **"calico-kube-controller"** pod logs with following command:

```
minikube kubectl -- logs logs <pod name> -n kube-system

for example:
logs calico-kube-controllers-585878fcf4-gnqfl -n kube-system
```

it should give following output
![minikube check pods calico logs](/img/jun-25/self-host-kubernetes-pt3/minikube-calico-kube-controller-logs.png)
<p>

## What to see on the next part ?

Let's summarize what we already do in this part as follows:

1. We learn how to create our first single node K8s with minikube
2. We learn how to add additional node / worker node to running cluster in minikube
3. We learn how to create minikube cluster with 2 node (Control-plane and worker)
4. We learn how to add "Role" label to the node we add
5. We learn how to interact with the K8s cluster within the minikube
6. We learn how to troubleshoot error in our calico deployment

Before I close this part, on the next part we will learn how to deploy "Production" cluster with IP we can access rather than minikube,
as minikube are meant to be development usage, not for production as stated on their page as follows:

![minikube production warning](/img/jun-25/self-host-kubernetes-pt3/minikube-warning.png)

Don't forget to stop your minikube cluster, if you no longer use it.
To clean up your minikube cluster execute following command:

```
minikube stop
minikube delete 
```

I hope this part help you learn how to setup local kubernetes cluster with minikube for testing or trying out kubernetes hands-on, see you on next post :smile:.
