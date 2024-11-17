Find ingress controller pod
```bash
kubectl get all --all-namespaces
```
Connect to ingress controller pod
```bash
kubectl exec -it -n ingress-nginx ingress-nginx-controller-bc57996ff-t467b -- /bin/bash
```
