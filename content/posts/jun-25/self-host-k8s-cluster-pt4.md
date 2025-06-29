---
ShowBreadCrumbs: true
searchHidden: false
author: ["Dino"]
title: "Self-host Kubernetes Cluster Pt.4"
date: 2025-06-30
tags: 
- kubernetes
- k8s
- microk8s
- self-host
- tech
- learning
---

## Intermezzo

![Intermezzo](/img/jun-25/self-host-kubernetes-pt4/self-host-kubernetes-pt4.png)

<p style='text-align: justify;'>

Meowdy :smiley_cat:, been while not update the article series and welcome to the 4th Part of self-host kubernetes !

This part we will setup the Low spec Production read K8s Clusters (usually used for Edge / IoT cases). I was planned to use microk8s by ubuntu but since the k0s (K-Zero-S) just graduate from incubation, I would like to try that one

Before we start, here are the specs I use for the Server I used as the Control-Plane and worker

- Ubuntu Minimized, Version 22.04.5
- Storage 20GB
- RAM 2GB with 4GB Swap
- [k0sctl](https://github.com/k0sproject/k0sctl#installation)

</p>

## Install and Configure k0s Cluster

<p style='text-align: justify;'>

I assume you already know how to prepare the server on your own, so I will just jump to the installation part here, so let's start with installing the **k0sctl** tool, which we will use to bootstrap our cluster.

1. Head to the Github of k0s to install the [k0sctl](https://github.com/k0sproject/k0sctl#installation)

2. SSH to your server and do update first before proceed with installation os k0s, since I use ubuntu usually I do as follows:

```
sudo apt update && sudo apt upgrade -y

## for minimized version usually you need to 
install additional tools like nano, curl, etc etc ... 
so dont forget to install it 

sudo apt install nano curl -y 
```

3. Wait for the k0s installation complete, should the installation complete will return similar output as follow:

![k0s installation complete](/img/jun-25/self-host-kubernetes-pt4/k0s-install-complete.png)

4. Next we will create bootstrap file for the controller, as follows:

```
mkdir -p /etc/k0s
k0s config create > /etc/k0s/k0s.yaml

## as note, since we create file on /etc/ folder which manage by root, 
change to root user first before create the k0s.yaml 
or simply put the k0s.yaml somewhere first 
then copy with sudo for easier way of executable
```

5. Once the k0s.yaml config file ready, you can check some of the configuration first, as follows:

```
apiVersion: k0s.k0sproject.io/v1beta1
kind: ClusterConfig
metadata:
  name: k0s
  namespace: kube-system
spec:
  api:
    address: 192.168.1.81
    ca:
      certificatesExpireAfter: 8760h0m0s
      expiresAfter: 87600h0m0s
    k0sApiPort: 9443
    port: 6443
    sans:
    - 192.168.1.81
    - fe80::be24:11ff:fe41:f0a0
    - k0s.<domain.tld> ## custom sans I add here 
  controllerManager: {}
  extensions:
    helm:
      concurrencyLevel: 5
  installConfig:
    users:
      etcdUser: etcd
      kineUser: kube-apiserver
      konnectivityUser: konnectivity-server
      kubeAPIserverUser: kube-apiserver
      kubeSchedulerUser: kube-scheduler
  konnectivity:
    adminPort: 8133
    agentPort: 8132
  network:
    clusterDomain: cluster.local
    dualStack:
      enabled: false
    kubeProxy:
      iptables:
        minSyncPeriod: 0s
        syncPeriod: 0s
      ipvs:
        minSyncPeriod: 0s
        syncPeriod: 0s
        tcpFinTimeout: 0s
        tcpTimeout: 0s
        udpTimeout: 0s
      metricsBindAddress: 0.0.0.0:10249
      mode: iptables
      nftables:
        minSyncPeriod: 0s
        syncPeriod: 0s
    kuberouter:
      autoMTU: true
      hairpin: Enabled
      metricsPort: 8080
    nodeLocalLoadBalancing:
      enabled: false
      envoyProxy:
        apiServerBindPort: 7443
        konnectivityServerBindPort: 7132
      type: EnvoyProxy
    podCIDR: 10.244.0.0/16
    provider: kuberouter
    serviceCIDR: 10.96.0.0/12
  scheduler: {}
  storage:
    etcd:
      ca:
        certificatesExpireAfter: 8760h0m0s
        expiresAfter: 87600h0m0s
      peerAddress: 192.168.1.81
    type: etcd
  telemetry:
    enabled: false
```

on step 4 I just make adjustment to add additional SANS, since I will have the cluster be accessable with domain I add, I still not sure will it work or not since this is the first time I setup the k0s Cluster

6. Once the configuratio is generated and adjusted, let's start the control-plane with following command:

```
sudo k0s install controller -c /etc/k0s/k0s.yaml --enable-worker --no-taints
sudo k0s start
```

you will see nothing on your terminal, but if you do see some error, try to check the error first before continue with next steps

7. Let's verify if the installation really complete with following commands:

```
sudo k0s status
```

you will see similar output as follow:

![k0s status](/img/jun-25/self-host-kubernetes-pt4/k0s-status.png)

8. Okay, so now we have the control-plane configured and running, let's verify some of the kubernetes services with following commands:

```
sudo k0s kubectl get nodes -A
sudo k0s kubectl cluster-info
sudo k0s kubectl get all -A -owide
```

it should output something similar as follows:

![k0s kubernetes component](/img/jun-25/self-host-kubernetes-pt4/k0s-kubernetes-components.png)

as we can see the cluster is already properly setup, now let's get the kubeconfig to be use from our local machine

9. To get the kubeconfig from k0s, use the following command:

```
sudo k0s kubeconfig admin
```

it will output the kubeconfig configuration to your terminal, the put is somehere in your local machine (e.g. ~/.kube/config)

10. Test the kubectl from your local machine after you update the kubeconfig, you should be able to access it like this

![kubectl from local machine](/img/jun-25/self-host-kubernetes-pt4/kubectl-local-machine.png)

</p>

## Install Load Balancer

<p style='text-align: justify;'>

So we just finished installed our K8s Cluster with K0s, but we dont actually finished yet with the installation, usually if we do setup on the cloud provider we will have the load balancer automatically provisioned to our cluster, but not quite on the self-host setup.

Now, let's configure Load Balancer to our K8s cluster, we will use MetalLB as our Baremetal Load Balancer for L2 Traffic that will go-in and go-out of our k8s cluster.

1. First let's create new namespace called **"metallb-system"**, once created we will deploy the metallb with kustomize in order for easier management of the resource we will do, use the following command to create the namespace:

```
kubectl create namespace metallb-system

## To verify the namespace creation use following command

kubectl get namespaces
```

the output should be similar like following capture:

![Kubectl create namespace metallb](/img/jun-25/self-host-kubernetes-pt4/kubectl-create-ns-metallb.png)

2. Next we will create folder containing our kustomize resources will be deployed, in this case for metallb requirement, following are the required resource will be created:

```
metallb-k0s/
├── ipaddresspool.yaml
├── kustomization.yaml
└── l2advertisement.yaml
```

Before continue, as we can see we have 3 yaml files, which are **'ipaddresspool.yaml, kustomization.yaml, l2advertisement.yaml'**, let me explain a bit why we need these 3 files to install metallb to our cluster ...

- **[ kustomization.yaml ]**

This file will read all our customization we need in-order to install the MetalLB to our cluster, which we will break down in a bit how the structure looks like as follows:

```
### kustomization.yaml
apiVersion: kustomize.config.k8s.io/v1beta1
namespace: metallb-system

resources:
  - github.com/metallb/metallb/config/native?ref=v0.15.2
  - ipaddresspool.yaml
  - l2advertisement.yaml
```

as we can see this is the structure for **kustomization.yaml**, which we will call necessary resources like the metallb installation resources, **ipaddresspool.yaml** and also **l2advertisement.yaml**

- **[ ipaddresspool.yaml ]**

This file will be our allocation IP Address used for the Load Balancer, it can be IP Public or IP Private, depending on your setup, but for this case since this is self-hosted on my home lab, I will be using IP Private, the structure looks likes as follwos:

```
apiVersion: metallb.io/v1beta1
kind: IPAddressPool
metadata:
  name: k0s-metallb-pool
  namespace: metallb-system
spec:
  addresses:
  - 192.168.1.85-192.168.1.90 #<-- kindly change to your IP Address allocation
  autoAssign: true
  avoidBuggyIPs: true
```

as we can see this is the structure of **ipaddresspool.yaml**, in this case we allocate IP Private from ***192.168.1.85 - 192.168.1.90***, also we assign the name of the IPAddressPool and namespace where we will deploy it.

there will be question, why there is **"autoAssign" and "avoidBuggyIPs"** in the configuration.

**"autoAssign"** is used when there is **"services"** that configured as LoadBalancer Type, for example like following:

```
apiVersion: v1
kind: Service

metadata: 
  name: nginx-ingress-controller
  namespace: nginx-ingress

spec: 
  allocateLoadBalancerNodePorts: true
  ClusterIP: x.x.x.x
  ClusterIPs: 
  - x.x.x.x
  externalTrafficPolicy: Cluster
  internalTrafficPolicy: Cluster
  ipFamilies: 
  - IPv4
  ipFamilyPolicy: SingleStack
  ports:
  - name: web
    nodePort: 32732
    port: 80
    protocol: TCP
    targetPort: web
  - name: websecure
    nodePort: 32180
    port: 443
    protocol: TCP
    targetPort: websecure
  selector: 
    app.kubernetes.io/name: nginx-ingress-controller
  type: LoadBalancer # <-- This is the part MetalLB will see when assigning IP
```

This is only example what will be check by MetalLB before auto assign the IP Address, we can also allocate it manually but will need some manual adjustment to it, so for now let's leave it as **"autoAssign"**

for the **"avoidBuggyIPs"**, it is related to the network devices that have protection against Smurf IP Address like ".0" or ".255", the detail can be read [here](https://metallb.io/configuration/_advanced_ipaddresspool_configuration/#handling-buggy-networks)

- **[ l2advertisement.yaml ]**

This file will handle all the L2 communication going-in and going-out of the cluster, since we don't use BGP for this setup, we only configure L2-Advertisement only, here are the structure looks like:

```
apiVersion: metallb.io/v1beta1
kind: L2Advertisement
metadata:
  name: k0s-metallb-l2adv
  namespace: metallb-system
spec:
  ipAddressPools:
  - k0s-metallb-pool
```

in this configuration, it will call the **"k0s-metallb-pool"** to handle the IP communication in L2 (Layer 2) way, so that the traffic can be go-in to the cluster and go-out of the cluster

3. Now we have all the requirement files, let's apply it to our cluster using following command:

```
# inside folder
kustomize build . | kubectl apply -f -

# outside folder
kustomize build  metallb-k0s/. | kubectl apply -f -  #<-- adjust to your folder name
```

Before execute this command makesure you already have **"kubectl"** and **"kustomize"** installed on your local machine

Once applied, it should look like this capture:

![kustomization apply](/img/jun-25/self-host-kubernetes-pt4/kustomization-apply-metallb.png)

Before move to the next part, let's check if all the config already applied correctly with following commands:

```
# In kubectl you can get multiple value from multiple sources like following

kubectl get pods,deploy,ipaddresspool,l2advertisement -n metallb-system 

```

it should output as follows:

![kubectl get](/img/jun-25/self-host-kubernetes-pt4/kubectl-get-output-metallb.png)

as we can see the IP we set on **"ipaddresspool.yaml"** is properly configured and the **"l2advertisement.yaml"** also pointing to the correct **"ipaddresspool"** name we set on the yaml file, but we are not sure yet if the configuration work expected or not, now let's create simple nginx pods and services to check if MetalLB working as expected.

let's prepare simple-nginx app like followings:

```
apiVersion: v1
kind: Pod
metadata:
  name: simple-nginx
  namespace: default
  labels:
    app: simple-nginx
spec:
  containers:
  - name: simple-nginx
    image: nginx:latest
    resources:
      limits:
        memory: "128Mi"
        cpu: "500m"
      requests:
        memory: "128Mi"
        cpu: "500m"
---
apiVersion: v1
kind: Service
metadata:
  name: simple-nginx-svc
  namespace: default
spec:
  ipFamilies:
  - IPv4
  ipFamliyPolicy:
  ports:
  - port: 80
    protocol: TCP
    targetPort: 80
  selector:
    app: simple-nginx
  type: LoadBalancer
```

then apply it with following command:

```
kubectl apply -f <your yaml file name>

e.g. kubectl apply -f simple-nginx.yaml
```

the output should like following:

![kubectl apply simple-nginx](/img/jun-25/self-host-kubernetes-pt4/k-apply-simple-nginx.png)

once applied, we can check the config is correctly applied with following command:

```
kubectl get pods,svc -n default
```

it should output something similar like following:
![kubectl get pods,svc -n default](/img/jun-25/self-host-kubernetes-pt4/simple-nginx-app.png)

as we can see from the **"service"** part, it have the load balancer type and configured with our allocated IP from the MetalLB IPaddressPool.

now the moment of truth, let's try to access our simple-nginx app with the load balancer IP we have allocated above.

it should be look like this on web or curl

![web access simple-nginx](/img/jun-25/self-host-kubernetes-pt4/web-access-simple-nginx.png)
![curl simple-nginx](/img/jun-25/self-host-kubernetes-pt4/curl-simple-nginx.png)
</p>

## What to see on the next part ?

Let's summarize what we already do in this part as follows:

1. We learn to setup production ready K8s Cluster with Baremetal Load Balancer
2. We test how the Baremetal Load Balancer work with simple nginx app
3. We configure and setup the Baremetal Load Balancer with MetalLB

Before I close this part, on the the next part we will learn about Ingress Controller and how it will help us with the application and the Load Balancer we already configured.

I hope this part help you learn how to setup the production ready kubernetes cluster with K0s, see you on next post :smile:.
