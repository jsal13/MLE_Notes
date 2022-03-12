# Kubelet

- Communicates with API server to see if pods have been assigned to nodes.
- Executes pod containers via the container engines.
- Mounts and runs pod volumes and secrets.
- Reports back to the API server the status of nodes.

_Podspec_: YAML file that describes a pod.

The kubelet takes this podspect and ensures containers described in those podspects are running and healthy.  It only manages containers managed by the API server.