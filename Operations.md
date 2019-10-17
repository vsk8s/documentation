# Operations

Find information about the daily operation and maintainence of our Kubernetes.

## API Auth

System administrators are best off using [TLS client
certificates](https://kubernetes.io/docs/reference/access-authn-authz/authentication/#x509-client-certs).

Authorisation is best done using RBAC.
[Here](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#default-roles-and-role-bindings)
all default roles are dscribed.

## Installation

The following packages are managed by us to enable Kubernetes on Debian:

* etcd

* containerd

* kubernetes-hyperkube

* kubernetes-hyperkube-kube-apiserver

* kubernetes-hyperkube-kube-controller-manager

* kubernetes-hyperkube-kube-proxy

* kubernetes-hyperkube-kube-scheduler

* kubernetes-hyperkube-kubectl

* kubernetes-hyperkube-kubelet

* cni-plugins

* calico-cni-plugin

* calico-ctl

* calico-typha

* calico-node

* k8router

* dns-api (only needed at ETH Zurich)

* python3-certbot-dns-eth (only needed at ETH Zurich)

* grpc-tools

* golang

## Upgrade

There is no plan to upgrade an existing cluster. Instead a new cluster with a
new version can be created and integrated to k8router (see there) and apps are
migrated to the new cluster.

## Important files and commands

Every daemon logs to `journal`! For investigations, always look there.

### On the worker nodes

* `ipvsadm` gives you the whole mapping of service IPs to pod IPs inside the
  cluster.

* `iptables-save` gives you the complete firewall configuration as managed by
  `kube-proxy`

* `ip route` shows all known routes to other workers and local pods.

* `/etc/bird/` contains all configuration regarding the bird BGP daemon

### On the routers

* `ipvsadm` gives you the whole mapping of externally exposed service IPs

* `iptables-save` gives you the complete firewall configuration

* `ip route` shows all known routes to other workers and service IPs

* `/etc/bird/` contains all configuration regarding the bird BGP daemon

* `/etc/haproxy/conf.d` contains all config files to be merged into haproxy
