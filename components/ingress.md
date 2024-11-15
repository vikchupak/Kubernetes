- Ingress (component)
  - Includes `Ingress controller` - set of pods that run on your node k8s cluster.
    - Entrypoint to cluster.
    - Many third-party implementations: https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/
      - The NGINX Ingress Controller for Kubernetes - is from k8s itself.
    - Evaluates and processes ingress rules.
    - Manage redirections.
 
```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: dashboard-ingress
spec: # Section responsible for configuration ingress controller
  ingressClassName: nginx # Explicitly targets a specific Ingress Controller (optional)
  rules:
  - host: dashboard.com
    http:
      paths:
        - path: /
          pathType: Prefix
          backend:
            service:
              name: kubernetes-dashboard
              port:
                number: 80
```
