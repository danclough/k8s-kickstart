# Kubernetes Kickstart
This repo contains instructions and examples to construct a bare-metal Kubernetes (often abbreviated `k8s`) cluster from the ground-up.  At the end of this guide, you will have a fully-functional Kubernetes cluster at your disposal.

This guide is *not* the easiest way to satisfy that objective.  Other wonderful projects like [Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/) and [Rancher k3s](https://k3s.io/) will get you there faster and with significantly less work!

Minikube is an official Kubernetes project, and will almost instantly give you a full single-node Kubernetes environment to play around or develop in.  `k3s` is a Kubernetes platform developed by Rancher, a company that sells commercial Kubernetes distributions and support.  `k3s` is a "slimmed-down-but-functionally-identical" Kubernetes distribution designed to run in environments with very few resources compared to a full-fat Kubernetes installation.  Both solutions simplify the process considerably by doing all the lifting for you.

## But Why?
As someone who has never worked with Kubernetes in a meaningful way, getting started was very difficult.  All the documentation *is* freely available - but with a subject as deep as Kubernetes, moving from concept to reality was quite a leap.  Some learn by reading, some by seeing, and others by *doing*.  If you are a hands-on learner like me, you may agree that the best way for you to conceptualize a new concept is to get your hands dirty.

While Kubernetes as a general concept is easy to explain, basic explanations don't do it justice.  Saying that Kubernetes is a "cluster for containers" is like describing a submarine as "a boat that sinks on purpose" - technically true, but miles away from real understanding.

Even though you can stand up a standards-conforming Kubernetes environment at the push of a button with Minikube or `k3s`, automating this process away deprives you of the ability to see for yourself how all the pieces stack up.

## How?
As a Kubernetes novice myself, I will walk you through standing up a bare-metal Kubernetes cluster.

## Components
Because almost every component of Kubernetes is interchangeable, choosing to build a cluster from scratch requires making a number of sometimes non-trivial decisions - what to use for networking?  For load balancing?  Storage?  Decision paralysis abounds.  Sometimes you just need a blueprint!

### kubeadm
```kubeadm``` is the official Kubernetes tool for quickly creating and expanding clusters.  The [kubeadm Reference Page](https://kubernetes.io/docs/reference/setup-tools/kubeadm/)] is an excellent place to start to learn more about kubeadm's features and options.

### Calico
[Calico](https://www.projectcalico.org/) is a networking solution for containers.  Calico is a cross-platform project that serves many other platforms besides Kubernetes, like Docker EE, OpenShift, and even bare-metal.  Calico provides essential network functions like routing and security policies between pods in a Kubernetes cluster and to the outside world.

### MetalLB
[MetalLB](https://metallb.universe.tf/) is a load-balancer implementation for bare metal Kubernetes clusters, using standard routing protocols.  Public clouds have their own load balancer service that can be called by Kubernetes to assign external IPs to any service that requests one.  Bare-metal clusters don't have this luxury and often require expensive alternatives like a hardware load balancer appliance which can become a "single point of failure".

MetalLB solves this problem by implementing a virtual IP address pool on top of Kubernetes, using standard protocls like ARP or BGP to announce the IPs to the rest of the network.

### Rook
[Rook](https://rook.io/) is a Kubernetes-based storage platform.  Rook takes existing storage platforms like Ceph or NFS and wraps them in a management layer that dynamically provisions a storage cluster on top of Kubernetes, using storage devices attached to the Kubernetes nodes themselves.

*Hyperconverged* means something that is local to the cluster and is pooled together and managed by the platform.  Companies like VMware and Nutanix offer hyperconverged storage platforms by combining the local storage across all their servers into a virtual pool, with redundancy baked in at the software level.

### Traefik
[Traefik](https://traefik.io/) is a modern HTTP reverse proxy and load balancer made to deploy microservices with ease.

While it is possible for every Kubernetes service to answer requests directly, this can become hard to manage very quickly.  Many Kubernetes cluster operators will use an *Ingress Controller* like Traefik, nginx, or HAProxy to intercept incoming requests and route them to the appropriate Kubernetes service.

Ingress controllers like Traefik can also handle security policies like permitting or denying certain IP ranges, enforcing HTTPS on certain URLs, and even offloading TLS so the containers behind each service don't need to handle TLS encryption themselves.

### cert-manager
[cert-manager](https://cert-manager.io/) is an automation tool that generates and manages TLS certificates for Kubernetes services.

Serving HTTPS traffic requires a valid certificate on the server - in this case, the reverse proxy that sits in front of your services.  Rather than requesting these manually and constantly updating them as the certificates expire, cert-manager will automatically generate a certificate signing request (CSR) and fulfill it using a number of possible providers - including Let's Encrypt, HashiCorp Vault, or even your own private CA.  cert-manager will even take care of renewing each certificate before it expires!

## Getting Started
Continue to [Step 1 - kubeadm and Calico](01-kubeadm-calico/) to get started!
