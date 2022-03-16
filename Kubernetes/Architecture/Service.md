# Service
Allows the communication between one set of deployments with another.

The IP never changes for the lifetime of the service.

Use a service to get pods in two deployments to talk to each other.

_You want to use a service whenever two pods need to talk to each other._

## Types of Services.
- Internal: IP is only reachable within the cluster.
- External: endpoint available through node ip: port (called NodePort).
- Load Balancer: Exposes application to the internet with a load balancer (available with a cloud provider).