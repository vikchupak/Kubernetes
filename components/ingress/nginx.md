Find ingress controller pod
```bash
kubectl get all --all-namespaces
```
Connect to ingress controller pod
```bash
kubectl exec -it -n ingress-nginx ingress-nginx-controller-bc57996ff-t467b -- /bin/bash
```
- Pod content
  ![image](https://github.com/user-attachments/assets/c0a3df90-7c0d-473b-9c6e-87bf6d18ae81)
- /etc/nginx/nginx.conf content
  - https://github.com/VIK2395/Kubernetes/blob/main/components/ingress/nginx.conf

```bash
kubectl get service -o yaml ingress-nginx-controller -n ingress-nginx > /tmp/my-ingress/ingress-nginx-controller.service.yaml
bash
