+++

Description = "Kubernetes with minikube"
Tags = ["kubernetes", "minikube"]
date = "2016-09-21T00:58:27-07:00"
title = "Kubernetes with minikube"

+++

[Minikube](https://github.com/kubernetes/minikube) is the easiest way to get yourself a dev friendly kubernetes setup.
You can get download it from [here](https://github.com/kubernetes/minikube/releases) and get yourself a kubernetes node
by doing just `minikube start`

<!--more-->

Of course, you need to have [Virtualbox](https://www.virtualbox.org/wiki/Downloads) installed on your host machine.

Once you get minikube binary, some immediate checks you can do

```
  $ minikube start
  Starting local Kubernetes cluster...
  Kubectl is now configured to use the cluster.

  $ minikube status
  minikubeVM: Running
  localkube: Running

  $ minikube ip
  192.168.99.104

  $ minikube dashboard
  Opening kubernetes dashboard in default browser...
```

However, it installs boot2docker based docker host on your Virtualbox. If you prefer CoreOS like me, you can install it by running
`minikube start --iso-url=https://github.com/coreos/minikube-iso/releases/download/v0.0.4/minikube-v0.0.4.iso`

You can also ssh into your minikube VM by running `minikube ssh`

`minikube` also lets you configure your docker environment easily so that you can connect to minikube's docker daemon directly from your
host machine.

```
  $ minikube docker-env
  export DOCKER_TLS_VERIFY="1"
  export DOCKER_HOST="tcp://192.168.99.104:2376"
  export DOCKER_CERT_PATH="/Users/karteek/.minikube/certs"
  export DOCKER_API_VERSION="1.23"
  # Run this command to configure your shell:
  # eval $(minikube docker-env)
```

And it configures `kubectl` automatically for you. So, you can start playing around with your kubernetes node immediately with in matter of minutes.

```
  $ kubectl get nodes
  NAME       STATUS    AGE
  minikube   Ready     3d

  $ kubectl cluster-info
  Kubernetes master is running at https://192.168.99.104:8443
  kubernetes-dashboard is running at https://192.168.99.104:8443/api/v1/proxy/namespaces/kube-system/services/kubernetes-dashboard

  To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```
