- Ingress (resource/component)
  - Includes `Ingress controller` - set of pods that run on your node k8s cluster.
    - Entrypoint to cluster.
    - Many third-party implementations: https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/
      - The NGINX Ingress Controller for Kubernetes - is from k8s itself.
    - Evaluates and processes ingress rules.
    - Manage redirections.
    - Usually exposes 2 ports: 80 and 443. Usualy we set service ports to 80 or 443 for them to map/forward to ingress ports.
 
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
```yaml
apiVersion: v1
kind: Service
metadata:
  name: kubernetes-dashboard
spec:
  selector:
    app: some-pods-selector
  ports:
    - protocol: TCP
    port: 80 # internal service port
    targertPort: 8081 # container/pod port
```
