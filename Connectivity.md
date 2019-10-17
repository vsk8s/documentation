# External Connectivity

Find information about the environment Kubernetes is embedded in for external
reachability.

## Routing

Kubernetes heavily relies on an external load balancer which is not part of
Kubernetes itself. Our setup uses haproxy, k8router and VRRP via keepalived for
this. We call our external load balancers "routers".

### Control Plane

Here the routers are responsible to serve the Kubernetes API highly-available.
Have a look at the [ansible role](https://github.com/vsk8s/ansible-router-k8s/blob/master/templates/10-k8s-master.conf.j2)
for how haproxy is configured for this.

### Data Plane

Pod and service subnets are announced via BGP between workers and routers. So to
route HTTP traffic to the cluster all requests are terminated by haproxy and
then forwarded to the ingress pods. This works because the IP address of the
ingress pods is routable from the routers because of BGP. The ingress will take
care of resolving the requested domain to a service IP which is routable on the
worker nodes and is forwarded on the same node via
[IPVS](http://www.linuxvirtualserver.org/software/ipvs.html) which resolves it
to a pod IP. This pod IP is routed to the correct worker node who delivers the
request to the application.

The approach for TCP/UDP/SCTP services is similar. The router is prepared via
IPVS to forward the request via IPVS to the service IP (which, as said before,
is routable from the routers) and forwarded to a worker. The rest of the
resolution is the same as above.

### Calico

Our network plugin is recommended to be set up inside the running kubernetes
cluster. We don't want this because it might introduce cyclic dependencies and
is far more difficult to debug. Therefore it runs under systemd. The services
emulate all files and environment variables, just like the process would find it
inside a real container.

## [K8router](https://github.com/vsk8s/k8router/)

Our routers support several Kubernetes clusters shared between several sets of
routers. Every router can route all service and pod IPs.

K8router is a program which observes ingress and ingress controllers in each
cluster and configures haproxy to dispatch every request to a specific ingress
to all clusters offering such an ingress.

Moreover it watches for `LoadBalancer` services and takes care of setting up
IPVS rules on the router to dispatch the requests to the correct cluster.

## TLS

Our cluster setup terminates TLS on the routers. TLS towards the backends is
supported but not deployed at the moment.

k8router is given a set of TLS keys and matches them up with the known ingresses
so every ingress can be served securely.
