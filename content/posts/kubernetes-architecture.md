+++

Description = "Kubernetes Architecture"
Tags = ["kubernetes"]
date = "2016-10-04T01:51:21-07:00"
title = "Kubernetes architecture"

+++

Kubernetes is fairly simple to install and configure. All you would need is
few VMs with docker (or rkt) installed. Once you understand the needed components
to get the system read, orchestrating them to sit at right place can be done in
many ways.

<!--more-->

As I mentioned in an [old post]({{< ref "posts/k8s.md" >}}), kubernetes has two
roles that you would need to know to start with

* Master
* Node (a.k.a Minion)

Before we get into each role, I would want to tell you about k8s components

1. **API Server** is the component all nodes would talk to to update their state
2. **Scheduler** is the component which schedules pods onto a node
3. **Controller manager** is technically what manages entire kubernetes cluster
  This is the component which manages nodes, replications controllers, deployments,
  endpoints etc of the k8s cluster. If you are running kubernetes on any cloud, I
  suggest you to always look for logs of your controller manager without failing
4. **kubelet** is the component/agent that interacts directly with docker and manages
  all the pods, images, containers, volumes etc.,
5. **kube-proxy** is the network proxy
6. **etcd** is the persistent storage for your k8s cluster's state

### Now, a master

A kubernetes master, would typically run API Server, Scheduler, Controller Manager,
and etcd. You can certainly put run them in a HA mode by running more than one
copy of them. Etcd data will be the data, you would have to take backups
(to take backup of k8s side of your cluster, you would need different strategy for
your application data backup)

You typically would run etcd2 as a service outside of kubernetes. And start API Server,
scheduler, controller manager on master nodes using kubelet. All of these components
ship in a single binary called *hyperkube*, and I use the [one](https://quay.io/repository/coreos/hyperkube?tab=tags)
from CoreOS (all my nodes are CoreOS too)

You can also run kubelet as a binary directly without using container by downloading
it from https://storage.googleapis.com/kubernetes-release/release/v1.3.8/bin/linux/amd64/kubelet.
This is not the preferred way, and binaries from this location can go away anytime.

### And, the node

On the k8s nodes (most likely more than one), you would run just kubelet and kube-proxy.
Kubelet on worker node will register with the API server. Controller Manager will
start to manage the node, scheduler will start scheduling pods onto the nodes, and
kube-proxy will set up load balancing as needed (iptables is default way).

You can check out the architecture from design documents of kubernetes [here](https://github.com/kubernetes/kubernetes/blob/master/docs/design/architecture.md).
Just remember to checkout the latest version.
