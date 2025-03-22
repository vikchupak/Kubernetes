Show ingress-nginx-controller service config in yaml (see ports)
```bash
kubectl get service -o yaml ingress-nginx-controller -n ingress-nginx
```

Output\
https://github.com/VIK2395/Kubernetes/blob/main/components/ingress/files/nginx-controller-service.yaml

Show ingress-nginx-controller service config standard (see ports)
```bash
kubectl get service ingress-nginx-controller -n ingress-nginx
```

![image](https://github.com/user-attachments/assets/1916a7af-17fc-4d15-b4c4-31c9aa7cc3b5)

So, ingress-nginx-controller-service is a NodePort service. Probably, the tunnel maps the localhost requests to the Node IP and Node Port.

In this case:
- 127.0.0.1:80 -> EXTERNAL_IP?:31544 `<node-ip>:<node-port>`. Then this is forwarded againt to the controller service on 80 port. Then the request gets to the controller pod (nginx instance) and the nginx manages the next routing inside the cluster.

Example of exposing custom node-port service
![image](https://github.com/user-attachments/assets/a4867bbb-7eb4-4aa3-a06d-dda6b94583e6)
