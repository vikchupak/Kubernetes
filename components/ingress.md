https://minikube.sigs.k8s.io/docs/start/?arch=%2Fwindows%2Fx86-64%2Fstable%2F.exe+download#Ingress

- Ingress (resource/component)
  - Includes `Ingress controller` - set of pods that run on your node k8s cluster.
    - Entrypoint to cluster.
    - Many third-party implementations: https://kubernetes.io/docs/concepts/services-networking/ingress-controllers/
      - The NGINX Ingress Controller for Kubernetes - is from k8s itself.
    - Evaluates and processes ingress rules.
    - Manage redirections.
    - Usually exposes 2 ports: 80 and 443. Usualy we set service ports to 80 or 443 for them to map/forward to ingress ports.
    - `http(s)://<host-mapped-to-service>:<80(443)>` in production host will resolve to real ip\
      After tunneling
       ```bash
       curl -H "Host: host-mapped-to-service" http(s)://127.0.0.1:<80(443)>
       ```
 
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

# Host header in Ingress

Accessing the dashboard directly using `http://192.168.49.2` **will not work** because the Ingress is configured to respond only to requests for the specific hostname `dashboard.com`. When you use `http://192.168.49.2`, the HTTP request won't include the `Host: dashboard.com` header that the Ingress controller expects, so the request will fail.

### Why Doesn't It Work?
1. **Host-Based Routing in Ingress**:
   - The `kubectl get ingress` output specifies a `HOSTS` field:
     ```
     HOSTS           ADDRESS        PORTS   AGE
     dashboard.com   192.168.49.2   80      37s
     ```
   - This means the Ingress is configured to serve traffic only when the HTTP request contains the `Host` header matching `dashboard.com`.

2. **Direct IP Access**:
   - When you visit `http://192.168.49.2` in your browser, the `Host` header in the HTTP request will be `192.168.49.2`, not `dashboard.com`. The Ingress won't recognize this and will return a 404 error or fail to route the request.

### How to Fix
If you don't want to modify your `/etc/hosts` file, you can use a browser trick:

1. **Access Using Curl (Optional)**:
   You can explicitly set the `Host` header with `curl` to test the connection:
   ```bash
   curl -H "Host: dashboard.com" http://192.168.49.2
   ```

2. **Set the Host Header in Your Browser**:
   Modern browsers don’t allow you to set the `Host` header directly, so modifying your `hosts` file is the best approach for browser access.

3. **Reconfigure the Ingress (Optional)**:
   If you want the Ingress to respond to direct IP access, you can add a wildcard host or remove the hostname restriction:
   ```yaml
   spec:
     rules:
     - host: ""
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
   This configuration allows the Ingress to accept any hostname, including direct IP access.

### Best Practice
The cleanest and most reliable way is to add `dashboard.com` to your `/etc/hosts` file as described earlier and use `http://dashboard.com` to access the dashboard.
