### **What Are Annotations in Kubernetes?**
- Annotations are **key-value pairs** used to attach metadata to Kubernetes objects (like Pods, Services, or Ingress).
- Unlike labels, **annotations are not used for selection or scheduling**—they store information that Kubernetes tools and controllers can use.  

---

### **Why Use Annotations?**
- 🔹 **Pass configuration to controllers** (e.g., AWS Load Balancer Controller, NGINX Ingress)  
- 🔹 **Attach external metadata** (e.g., URLs, monitoring info)  
- 🔹 **Provide debugging or operational info** (e.g., deployment history, logs)  
- 🔹 **Integrate with external tools** (e.g., CI/CD pipelines, Helm, service meshes)  

---

### **Example: Annotations in an Ingress Resource (For AWS ALB)**
If you use the **AWS Load Balancer Controller**, annotations define **how AWS should configure the ALB**.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: my-ingress
  annotations:
    alb.ingress.kubernetes.io/scheme: internet-facing  # Creates a public ALB
    alb.ingress.kubernetes.io/target-type: ip  # Targets individual pods
    alb.ingress.kubernetes.io/listen-ports: '[{"HTTP": 80}, {"HTTPS": 443}]'  
spec:
  ingressClassName: alb
  rules:
  - host: myapp.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: my-app
            port:
              number: 80
```

### **Example: Annotations in an NGINX Ingress (For EKS)**
If using **NGINX Ingress Controller**, annotations control **NGINX-specific settings**.

```yaml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: nginx-ingress
  annotations:
    nginx.ingress.kubernetes.io/rewrite-target: /  # Rewrites all traffic to root path
    nginx.ingress.kubernetes.io/proxy-body-size: 10m  # Allows 10MB file uploads
spec:
  ingressClassName: nginx
  rules:
  - host: myapp.example.com
    http:
      paths:
      - path: /
        pathType: Prefix
        backend:
          service:
            name: my-app
            port:
              number: 80
```

### **Annotations vs. Labels**
| Feature | Labels | Annotations |
|---------|--------|------------|
| Used for **object selection**? | ✅ Yes | ❌ No |
| Used by controllers & tools? | ❌ No | ✅ Yes |
| Stores additional metadata? | ❌ No | ✅ Yes |
| Example Usage | `app: nginx` for selectors | `alb.ingress.kubernetes.io/scheme: internet-facing` |

---

### **Conclusion**
- **Annotations** are metadata key-value pairs used by controllers and external tools.
- **They do not affect Kubernetes scheduling or selection** (unlike labels).
- **Common use cases**: AWS ALB settings, NGINX configs, monitoring info, debugging.
