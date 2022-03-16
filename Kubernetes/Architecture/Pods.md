# Pods

Pods are the smallest unit of organization in K8s.

## Pod States
- Pending
- Running
- Succeeded (all containers have exited with 0)
- Failed (all containers have exited AND at least one has failed)
- CrashLoopBackOff (a container has failed to start, it tries a ton of times to start it but it can't get it to work)