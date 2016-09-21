+++

Description = "Kubernetes on AWS"
Tags = ["kubernetes", "aws"]
date = "2016-09-21T00:58:27-07:00"
title = "k8s on AWS"

+++

Been long time that I wrote anything remotely useful for others. Thought to change that by writing about a tool that I have been using a lot lately - [Kubernetes](http://kubernetes.io/), an awesome container orchestration tool

<!--more-->

I have been using kubernetes for work from past few months on AWS, I've learnt few things about kubernetes the hardway.
One of the most common complaints I keep reading about kubernetes is that documentation is bad. I would agree to that half-heartedly as
documentation is not bad, but more or less not greatly organized. There is a lot of information available over their
[github account](https://github.com/kubernetes/kubernetes/tree/master/docs), just waiting to be found and understood.
This is a small attempt to simplify connect few dots. This is by no-means a guide or tutorial or
a walk through. This is purely about my journey in getting it to work the way I wanted.

Before you continue and waste more of your time reading this, you should certainly read [this](http://kubernetes.io/docs/whatisk8s/) if you dont know what is kubernetes. Also, this post doesn't cover any code or configurations, just hints and links to guides/tools that helped me.

## Expectations

My expectations on k8s (kubernetes) deployment/cluster that I wanted to do were simple

  1. High available components
  2. Stable to use for production
  3. Self healing if something happens
  4. Easy to deploy cluster, repeatedly

Before going into details, these are the types of nodes you would have in your k8s cluster

  1. Master is a server that manages the cluster. There can be more than one of these machines.
  2. Minion is a server that runs your containers. There will always be more more than one or more of these machines.

These are the roles of servers in our kubernetes cluster. A node can play both roles if needed.
Typically in a dev environment, you can have one server playing both the roles (a.k.a 1-node k8s cluster).
And in a production environment, you would want multiple servers playing these roles.

## Journey

### HA

As my target was to deploy this on AWS, the obvious place of starting my journey was [AWS getting started guide](http://kubernetes.io/docs/getting-started-guides/aws/) by kubernetes team. And it took less than 2 minutes to
realize that guide is not very helpful to me and doesn't meet my #1 requirement of (Highly available components)

It shows on how to install kubernetes on a single AWS availability zone, with 1 master and `n` minions.

  - When master goes down, cluster cannot be controlled
  - When AZ goes down, entire cluster is down.

And then I went to go read their [guide on creating High-Availability Cluster](http://kubernetes.io/docs/admin/high-availability/).
I realized that a good start would be to create a `3`-master and `n`-minion cluster

### Stability

`Stability` is something I enjoyed from kubernetes without putting any effort. It just worked out of the box. Kudos to Kubernetes team.

### Self-healing

I wanted my cluster to heal itself if one of the minion goes down, or even when a master goes down. I achieved this easily using AWS Auto Scaling groups. My process was something like this

  1. Create an ASG for masters, put them behind an internal ELB (load balancer)
  2. Create an ASG for minions, make them talk to ELB of masters

Now, when a node goes down, AWS auto scaling group will automatically replace it. But, for this to work, the new node that is being added
by Autoscaling group should come with configuration when it boots. I got this idea from a tool called [kube-aws](https://github.com/coreos/coreos-kubernetes/tree/master/multi-node/aws) from CoreOS team. I managed to do pre-configuration of this part by using [cloud-init](https://cloudinit.readthedocs.io/en/latest/).

### Easy to deploy, repeatedly

There is a tool which I like for managing my infrastructure components. It's a very nice tool which helps you deal with your infrastructure
as if it's code - [Terraform](https://www.terraform.io/)

Now that I already know what I need to create, Using terraform, I simply created

  1. Different Launch Configurations for master ASG and minion ASG
  2. Internal Master ELB
  3. Cloud Init config for masters
  4. Cloud Init config for minions, pointing towards internal master ELB
  5. LC for masters using master's cloud init, and LC for minions using their cloud init
  6. ASG using appropriate LCs

End result was a k8s cluster spanning across three AZs with multiple masters, multiple minions.


## Conclusion

Even though, I managed to meet my requirements fairly easily, thanks to the vast kubernetes community, and articles/tools they wrote.
Kubernetes can look daunting when you see their documentation, but its easy to setup and lovely to use once you understand components of it.

You should certainly read this [super useful tutorial](https://github.com/kelseyhightower/kubernetes-the-hard-way) by [@kelseyhightower](https://twitter.com/kelseyhightower)., and go through these HN threads - [1](https://news.ycombinator.com/item?id=12022215), [2](https://news.ycombinator.com/item?id=12323187) related to kubernetes.
