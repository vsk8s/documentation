# Ansible

The recommended setup is to check out all needed roles as submodules. Have a
look at `ansible/` for a sample setup. This sample setup has not been tested and
should rather give an idea!

## Variables

Here important variable lists are presented with their explanation.

### Kubernetes-global variables

Each kubernetes cluster has information which has to be shared between so many
roles that they were established as convention. They are:

#### `kubernetes_cluster_cidr`

CIDR all other IPs except for `kubernetes_ha_ip_intern` are contained in.

#### `kubernetes_service_cidr`

Network for service IPs.

#### `kubernetes_dns_service_ip`

Service IP of the DNS service.

#### `kubernetes_api_service_ip`

Service IP of the Kubernetes API. Make this the first IP inside
`kubernetes_service_cidr`.

#### `kubernetes_ha_ip_intern`

External IP of the Kubernetes API. We are aware of the discrepancy between the
variable name stating `intern` and the external nature. This IP is external to
the Kubernetes cluster's service subnet but still intern to the cluster routers.

#### `kubernetes_ha_domain_intern`

DNS name leading to `kubernetes_ha_ip_intern`. Only needs to be resolvable
behind the routers.

#### `kubernetes_ha_domain_extern`

DNS name globally leading to the Kubernetes API.

#### `kubernetes_cluster_domain`

Domain used for DNS names inside the cluster.

#### `kubernetes_autonomous_system_number`

BGP AS number of this cluster.

#### `kubernetes_ansible_worker_group`

Name of the worker group as defined in the inventory or similar.

#### `kubernetes_ansible_master_group`

Name of the master group as defined in the inventory or similar.

#### `kubernetes_ansible_router_group`

Name of the router group as defined in the inventory or similar.
