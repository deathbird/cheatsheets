```bash
#Show details of specific pod
kubectl  describe pod <pod name> -n <namespace-name>

# View logs for specific pod
kubectl  logs <pod name> -n <namespace-name>

# Why a pod is crashing
kubectl get events --sort-by=.metadata.creationTimestamp

```
