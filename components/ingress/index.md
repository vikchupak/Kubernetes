An **NGINX Ingress Controller** is a Kubernetes-native way to manage HTTP and HTTPS traffic to services within a cluster. It uses an NGINX instance to route incoming requests to the appropriate backend services based on rules defined in **Ingress resources**.

## **Architecture of the NGINX Ingress Controller**

### Key Components:
1. **Ingress Resource**:
   - Defines the routing rules for your services (e.g., `/foo` routes to `foo-service`, `/bar` routes to `bar-service`).

2. **Ingress Controller**
   -  **Ingress (nginx) Controller Service**:
       - Exposes the Ingress Controller Pod to external traffic.
       - Commonly configured as a `LoadBalancer` or `NodePort` service.
   - **Ingress (nginx) Controller Pod**:
       - Runs the NGINX instance.
       - Watches for changes in Ingress resources and updates the NGINX configuration dynamically.

## **How It Works**
1. The **Ingress Controller Pod** listens for changes in Ingress resources.
2. It updates the internal NGINX configuration dynamically.
3. When traffic arrives at the Ingress Controller (via its Service), NGINX routes it to the appropriate backend based on Ingress rules.

*****

- **Ingress Resource**:
  - Specifies the rules but doesn't expose ports itself.
  - Relies on the Ingress Controller for actual traffic handling.

- **Ingress Controller Service(external)**:
  - Listens on **ports 80 and 443**.
  - Exposes the Ingress Controller Pod to external traffic.

- **NGINX Ingress Controller Pod**:
  - Internally listens on **ports 80 and 443**.
  - Processes requests and forwards them to the backend services.

---

- **Ingress Rule**:
  ```yaml
  apiVersion: networking.k8s.io/v1
  kind: Ingress
  metadata:
    name: example-ingress
  spec:
    rules:
      - host: example.local
        http:
          paths:
            - path: /foo
              pathType: Prefix
              backend:
                service:
                  name: foo-service
                  port:
                    number: 80
  ```

- **Ingress Controller Service**:
  ```yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: ingress-nginx-controller
  spec:
    ports:
      - name: http
        port: 80
        targetPort: 80
      - name: https
        port: 443
        targetPort: 443
  ```

### **How Traffic Flows in Kubernetes with an Ingress Resource**
1. **Client Sends a Request**:
   - A client sends an HTTP/HTTPS request to the **Ingress resource's external IP or domain name** (e.g., `http://example.local` or `https://example.local`).
   - This IP is typically the external IP of the **Ingress Controller's Service**.

2. **Traffic Reaches the Ingress Controller**:
   - The **Ingress resource** does not directly handle requests. Instead, it acts as a set of rules.
   - The **Ingress Controller** (like NGINX) is the actual application that processes these rules.
   - The request first arrives at the **Ingress Controller's Service**, which is usually of type:
     - **LoadBalancer**: Exposes an external IP.
     - **NodePort**: Exposes a high port on each Kubernetes node.
   - The Service forwards the request to the NGINX Ingress Controller Pod.

3. **Ingress Controller Processes the Request**:
   - The NGINX Ingress Controller Pod:
     1. Parses the **Ingress resource** rules (e.g., `/foo` routes to `foo-service`, `/bar` routes to `bar-service`).
     2. Dynamically updates its internal NGINX configuration (`nginx.conf`) based on these rules.

4. **Forwarding to Backend Services**:
   - After determining the appropriate backend service:
     - The Ingress Controller forwards the request to the backend **Service** (e.g., `foo-service` or `bar-service`).
     - The **Service** routes the request to one of its **Pods**.

5. **Response Flow**:
   - The backend Pod sends the response back to the NGINX Ingress Controller.
   - The Ingress Controller sends the response to the client.

---

### **Illustration of Traffic Flow**
```
Client --> Ingress (IP/DNS) --> Ingress Controller Service (80/443) --> NGINX Ingress Pod -->
  --> Backend Service (e.g., foo-service) --> Pod (e.g., foo-app) --> Response to Client
```

### **Summary**
1. **Ingress Resource**: Defines rules (e.g., routes and backends).
2. **Ingress Controller Service**: Exposes the NGINX Ingress Controller Pod to external traffic on ports 80/443.
3. **Ingress Controller Pod**: Implements the rules and forwards traffic to backend services.

*****

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
