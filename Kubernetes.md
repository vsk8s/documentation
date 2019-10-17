# Kubernetes

Find information about the architecture inside Kubernetes itself.

## Master

They run

* [etcd](https://etcd.io/)

* kube-apiserver

* kube-scheduler

* kube-controller-manager

* calico-typha

All services run as systemd service and log to journal.

### etcd

A distributed key-value database used by the kube-apiserver as backend

### kube-apiserver

The core component of Kubernetes. This is the process serving the Kubernetes
API.

The kube-apiserver is granted `system:kubelet-api-admin` so they can access logs
of pods. This makes the kube-apiserver also a client towards the kubelets.

### kube-scheduler

Responsible for assigning pods to worker nodes

### kube-controller-manager

Collection of managers performing many different tasks to "move the current
state towards the desired state"
[Source](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-controller-manager/)

### calico-typha

A proxy between Calico Felix and the kube-apiserver.  This is the [recommended
setup](https://github.com/projectcalico/typha) for large scale deployments as we
anticipate.

## Worker

They run

* kubelet

* kube-proxy

* calico-felix

* calico-confd

* containerd

Note that workers don't run docker but instead they run containerd which is the
common base service, docker also relies on. This reduces dependency on the
unreliable docker API.

### kubelet

Take care of starting pods assigned to this node.

### kube-proxy

Manage connectivity of pod and service IPs on this node. It manages
[IPVS](http://www.linuxvirtualserver.org/software/ipvs.html) and iptables
firewall rules.

### calico-felix and calico-confd

Together they take care of the nodes connectivity. They assign IP blocks to
nodes, manage the BGP daemon [bird](https://bird.network.cz/) and talk to
calico-typha.

### Containerd

The program actually providing containers for pods.

## Network

To connect pods to the network we have decided for [Project
Calico](https://www.projectcalico.org/). It uses BGP to announce IP subnets
containing pods. The worker and routers form a single AS.  See [
Connectivity](https://github.com/vsk8s/documentation/blob/master/Connectivity.md) for
more on networking.

Calico consists of two programs. `calico-typha` is deployed on our kubernetes
master nodes and acts as proxy for the Kubernetes API towards `calico-node`
daemons.

## Ingress

We use [nginx](https://kubernetes.github.io/ingress-nginx/) as ingress.

