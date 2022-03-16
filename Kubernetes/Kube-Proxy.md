# Kube-Proxy

The network proxy.

- Process running on every worker nodes.
- Reflects services as defined on each node, and can do network stream or round-robin forwarding across a set of backends.
- Service cluster IPs and ports are current found through Docker `--link` compatible environ vars specifying ports opened by the service proxy.

- User Space mode.
- IPTables mode.
- IPVS mode (alpha).

Kube-proxy watches the API server for the addition and removal of services.