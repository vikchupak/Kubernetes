# DNS

- https://minikube.sigs.k8s.io/docs/handbook/addons/ingress-dns/#Linux
- https://minikube.sigs.k8s.io/docs/handbook/accessing/
  ![image](https://github.com/user-attachments/assets/bbe8b263-fb8d-498e-80a1-6b7fabf3d734)
  ![image](https://github.com/user-attachments/assets/c860f9e9-ebae-4dfd-a816-c940fd79bea6)

# Minikube

Useful links
- https://kubernetes.io/
- https://minikube.sigs.k8s.io/docs/start/?arch=%2Fwindows%2Fx86-64%2Fstable%2F.exe+download
- https://kubernetes.io/docs/tasks/tools/
- https://kubernetes.io/docs/reference/kubectl/kubectl/
- https://kubernetes.io/docs/reference/kubectl/

Minikube
- Local Kubernetes. One node cluster where `Control Plane Processes` and `Worker Processes` run on one node/machine.
- Has docker container runtime pre-installed.
- Uses/installs `kubectl` native Kubernetes command-line tool to run commands against Minikube Kubernetes clusters  as Minikube has kubectl as a dependency.
- Can be deployed/run as a VM, a container, or bare-metal on your host.
  - Minikube can be run as a docker container(if the driver is docker).
  - At the same time, uses pre-installed docker container runtime to run containers inside Minikube cluster.

```bash
minikube status
```
```bash
minikube start
```
```bash
# Start k8s nginx ingress controller implementation
minikube addons enable ingress
```
- https://minikube.sigs.k8s.io/docs/handbook/addons/ingress-dns/#Linux

**In Minikube**, LoadBalancer service and Ingress require a tunnel to allow external access to the cluster.
- https://minikube.sigs.k8s.io/docs/handbook/accessing/
- https://minikube.sigs.k8s.io/docs/commands/tunnel/
```bash
# For Ingress
minikube tunnel
# To see assigned port
kubectl get service <service-name>
```
```bash
# For LoadBalancer, NodePort services
minikube service <service-name> --url
# Outputs service access url with assigned port
```

## `minikube tunnel` vs `minikube service <service-name>`

Both commands are used to expose services in Minikube, but they work differently.  

---

## **🔹 `minikube tunnel`**
✅ **Creates a network tunnel** to expose **LoadBalancer** services on your local machine.  
✅ Requires **root (sudo) privileges** because it modifies the system routes.  
✅ Runs **persistently** in the terminal until manually stopped (`Ctrl + C`).  
✅ Works with **LoadBalancer services only** (not NodePort).  

### **Example Usage:**
```sh
minikube tunnel
```
- This exposes **all LoadBalancer services** in your Minikube cluster.  
- You need to open a separate terminal to run other commands while it is running.  

### **When to Use?**
- When your service is of **type: LoadBalancer** and you need to expose it on your local machine.  
- Example service definition:
  ```yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: my-service
  spec:
    type: LoadBalancer
    ports:
      - port: 80
        targetPort: 8080
    selector:
      app: my-app
  ```

---

## **🔹 `minikube service <service-name>`**
✅ **Finds and exposes a service** running in Minikube.  
✅ Works with **NodePort and LoadBalancer services**.  
✅ Automatically **opens a browser** (unless `--url` is used).  
✅ Runs in the foreground, but can be closed manually.  

### **Example Usage:**
```sh
minikube service mongo-express-service --url
```

---

## **🔹 Key Differences**
| Feature               | `minikube tunnel` | `minikube service <service-name>` |
|----------------------|----------------|--------------------------|
| Works with **LoadBalancer** | ✅ Yes | ✅ Yes |
| Works with **NodePort** | ❌ No | ✅ Yes |
| Requires **root/sudo** | ✅ Yes | ❌ No |
| Opens in a browser | ❌ No | ✅ Yes (unless `--url` is used) |
| Exposes **all LoadBalancer services** | ✅ Yes | ❌ No (only one service) |
| Runs **persistently** | ✅ Yes | ❌ No (stops when closed) |

---

## **❓ Which One Should You Use?**
- If your service is **NodePort**, use `minikube service <service-name>`.  
- If your service is **LoadBalancer**, use **either**:
  - `minikube tunnel` (for all LoadBalancer services)  
  - `minikube service <service-name>` (for a single service)  
