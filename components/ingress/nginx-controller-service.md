Show ingress-nginx-controller service config in yaml (see ports)
```bash
kubectl get service -o yaml ingress-nginx-controller -n ingress-nginx
```

Output\
https://github.com/VIK2395/Kubernetes/blob/main/components/ingress/nginx-controller-service.yaml

Show ingress-nginx-controller service config standard (see ports)
```bash
kubectl get service ingress-nginx-controller -n ingress-nginx
```

![image](https://github.com/user-attachments/assets/1916a7af-17fc-4d15-b4c4-31c9aa7cc3b5)

So, ingress-nginx-controller-service is a NodePort service. And probably, the tunnel maps the localhost requests to the service IP and ports.

In this case:
- 127.0.0.1:80 -> EXTERNAL_IP?:31544 `<node-ip>:<node-port>`. Then this are forwarded againt to the controller service to on 80 port. Then the request gets to the controller pod (nginx instance) and the nginx manages the next routing inside the cluster.
