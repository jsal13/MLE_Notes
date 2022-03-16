# ReplicaSet

This ensures a specified number of replicas for a pod are running at all times.

If the number of pods is less than what the RS expects, then the RS controller starts up a new pod.

RSs are defined inside a deployments.