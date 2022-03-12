# Kubernetes

## Concepts

### Nodes and Control Planes
- Clusters contain two types of resources:
	- **Control Plane** which coordinates the cluster.
		- Schedules applications.
		- Maintains application desired state.
		- Scaling.
		- Rolling out updates.
	- **Nodes** which run the applications.
		- VM or Physical Computer that serves as a worker machine.
		- Each has a **Kubelet**, an agent for managing the node and talking to the _Control Plane_.
		- Has tools for handling container operations like Docker.
	
A cluster should have _at least three nodes!_

When you deploy to K8s:
- The control plane is told to start the app's containers.
- The control plane schedules the containers to run on the cluster's nodes.
- The nodes talk with the control plane using the Kubernetes API, which is exposed by the Control Plane.
- End users may also interact with the K8s API.

### Deployments
- Must create K8s [[Deployment]] Configuration.
	- How to create + update instances of the app.

After this, the Control Plane schedules the app instances in the Deployment to run on the nodes in the cluster.

Once the instances are created, a **K8s Deployment Controller** continuously monitors the instances.  The K8s Deployment Controller tries to replace any broken instances with other nodes in the cluster --- an attempt at self-healing.

A [[ReplicaSet]] ensures a specified number of replicas for a pod are running at all times.

By default, _pods cannot talk to the outside world_ --- they can only communicate on their own internal, private network.  We can interact with them using kubectl, for example.  Note that the API server automatically creates an endpoint for each pod based on its name.  We can get these with a kubectl command as well.

[[Pods]] in K8s are abstractions that represent a group of app containers (like docker!) and some shared resources such as:
- Shared storage (volumes)
- Networking (unique cluster IP)
- Info about how to run each container (container image version, ports, etc.) 

For example, a Pod might include both a nodejs app container and also a container that feeds the app data via some other mechanism. 

### Services and Labels
A **Service** routes traffic across a set of pods.  This lets pods die and replicate if necessary.  Services use **labels and selectors** to match sets of pods.  (Tags, etc.).





## Basic Commands

```shell
kubectl get [nodes|deployments|pods|replicaset] [-o wide|json|...]

# Describe a resource
kubectl describe [pods|nodes|deployments|...]

# Print the logs from a container in a pod.
kubectl logs

# Execute a command in a container in a pod.
kubectl exec

# Creates Deployment
kubectl create deployment name-of-deployment --image=name-of-image:tag

# Get a proxy for communication with the Pods.
kubectl proxy

# Expose a port.
kubectl expose label-name/name-of-service --type="NodePort" --port port_num
kubectl expose deployment/kubernetes-bootcamp --type="NodePort" --port 8080  #Ex.

# We can label a pod with
kubectl label pod pod-name key=value

# Delete a service:
kubectl delete service [-l app=kubernetes-bootcamp] # by label.

# Scale up deployment.
kubectl scale label-name/name-of-service --replicas=desired_num
kubectl scale deployments/kubernetes-bootcamp --replicas=4  #Ex.

```