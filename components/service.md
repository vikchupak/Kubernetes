## Service types

- **ClusterIP**
  - Default. Acts as a load-balancer.

- **NodePort**
  - External via node-ip + node-port.
  - `spec.ports.nodePort` to make service accessible externally.
  - `http://<any-node-ip>:<node-port>`; See LoadBalancer. Exactly the same setting.
    ![image](https://github.com/user-attachments/assets/8c4fcbf8-611e-4ddd-86c3-786d17ad449b)
  - **A service spans accross all nodes**
  - **When NodePort service created, it also creates ClusterIP services and routes trafic from the NodePort to the ClusterIP services. ("Wraps" ClusterIP)**
  - A NodePort provides a simple way to access a service **during development or debugging** without needing a more complex setup like Ingress or LoadBalancer.

- **LoadBalancer**
  - External.
  - **It is an abstraction service!!! It doesn't really accepts trafic on k8s level, unlike nodePort or clusterIP**
  - Exposes a service externally by provisioning a cloud provider's load balancer (e.g., AWS ELB, GCP LB, or Azure LB).
    - LoadBalancer services in Kubernetes are primarily designed for use with cloud providers.
  - `http://<external-load-balancer-ip(unique)>:<node-port>` in produnction; after tunnel maps on `http://127.0.0.1:<tunnel-port>`
    - command for tunnel `minikube tunnel`; then `kubectl get services <service-name>` > EXTERNAL-IP
    - or `minikube service <service-name>` > see logs or `kubectl get services <service-name>` > EXTERNAL-IP
  - Each service with type LoadBalancer gets its own external load balancer.
  - The external load balancer forwards traffic to the NodePort on the nodes.
  - **When LoadBalancer service created, it also creates NodePort services and routes trafic from the LoadBalancer to the NodePort services.  ("Wraps" NodePort)**
    - So the flow is
      1. External request → LoadBalancer (External IP)
      2. LoadBalancer → NodePort (< Random-NodeIP >:32000)
      3. NodePort → ClusterIP (10.96.0.1)
      4. ClusterIP → Pod IP (e.g., 10.244.1.2)
  - Each service of `type LoadBalancer` gets **its own** external load balancer that forwards traffic **only** to its service.
    - **Service A (type: LoadBalancer)** gets its own external load balancer A. Load balancer A **only forwards traffic to Service A**.
    - **Service B (type: LoadBalancer)** gets its own external load balancer B. Load balancer B **only forwards traffic to Service B**.

- **Headless**
  - Doesn't have own static/stable service IP. Returns the service's pod IPs instead.
  - `spec.clusterIP: None` to make a service Headless. `It is still ClusterIP type`.
  - Usefull for stateful apps.
 
## LoadBalancer service vs cloud load balancer

### `Is LoadBalancer service replaced or wrapped by cloud load balancer?`

The **Kubernetes LoadBalancer Service** is **wrapped by** the cloud provider's external load balancer, not replaced.  

### **How It Works:**
1. **You create a `LoadBalancer` service** in Kubernetes.
2. **Kubernetes requests an external load balancer** from the cloud provider (e.g., AWS ELB, GCP Load Balancer, Azure LB).
3. The **cloud provider provisions an external load balancer** and assigns it a **public IP**.
4. The cloud load balancer forwards traffic to a **NodePort service** running inside Kubernetes.
5. **NodePort then routes traffic to the ClusterIP**, which sends it to the appropriate pods.

### **Key Concept: The LoadBalancer Service is a K8s Abstraction**
- The **actual traffic routing** is handled by the **cloud provider's** external load balancer.
- Kubernetes **still maintains a `LoadBalancer` service object**, but it **doesn't replace the cloud balancer**—it just integrates with it.

### `So, LoadBalancer service is NOT really accepting requests?`

Yes, you are absolutely right! The **Kubernetes LoadBalancer Service itself does not directly accept requests**. Instead, the external cloud load balancer **bypasses it** and sends traffic directly to the NodePort.  

### **Flow Summary**
1. **Client sends request** to the cloud load balancer's **public IP**.  
2. The **cloud provider’s load balancer** forwards the request to the Kubernetes **NodePort** on one of the nodes.  
3. The **NodePort service** then routes traffic to the **ClusterIP**, which distributes it to the **Pods**.  

### **What Happens to the LoadBalancer Service?**
- The `LoadBalancer` **service object** in Kubernetes acts as a **declarative abstraction**, telling Kubernetes to provision an **external** load balancer.
- But **it does not process network traffic itself**—it simply ensures that the cloud provider provisions and manages an actual external load balancer.

### **Does the LoadBalancer Service Ever See Traffic?**
No, the Kubernetes **LoadBalancer service is just an API object**—the actual traffic **only flows through**:
1. The **cloud provider's external load balancer**.
2. The **NodePort service** running on Kubernetes worker nodes.
3. The **ClusterIP service**, which distributes traffic to Pods.

### `on-promises load balancers`

- On on-premises (self-hosted Kubernetes), where there is no cloud provider, the LoadBalancer service does not automatically provision an external load balancer.
- But, you can configure a traditional load balancer like NGINX to forward traffic to the Kubernetes NodePort service

## LoadBalancer vs Ingress

So, there are different ways to setup cluster
1. Using cloud load balancer without ingress
2. Using cloud load balancer with ingress
---   
3. On-promises with ingress and setting external load balancer(reverse proxy)
4. On-promises without ingress setting external load balancer. (Very difficult for big production projects and not efficient way)
   - This would mean that the external load balancer would have to route trafic using IP based routing using `node-ip:node-port`
       - Cloud providers manage this under the hood

See 
- https://github.com/vikchupak/Kubernetes/blob/main/components/ingress/cloud.vs.internal/index.md
- https://www.eksworkshop.com/docs/fundamentals/exposing/aws-lb-controller

---

- ALB works at 7 OSI layer **(Host based routing)**
- NLB works at 4 OSI layer **(IP based routing)**

## Config files

- Internal service. It also acts as a load-balancer despite the name.
  ```yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: mongodb-service
  spec:
    selector:
      app: mongodb
    # type: ClusterIP
    ports:
      - protocol: TCP
      port: 27017
      targertPort: 27017
  ```
  - ClusterIP is an implicit default type
  - ClusterIP exposes the service on a cluster-internal IP
- External service
  ```yaml
  apiVersion: v1
  kind: Service
  metadata:
    name: mongodb-express-service
  spec:
    selector:
      app: mongo-express
    type: LoadBalancer # This makes the service external, NOT the best type name
    ports:
      - protocol: TCP
      port: 8081 # internal service port
      targertPort: 8081 # container/pod port 
      nodePort: 30000 # external service port. Must be in range 30000-32767
  ```
  - LoadBalancer assigns in addition an external IP
  - In **Minikube**, the external IP is NOT assigned immediately. To "force" Minikube expose the external IP, we need to run the command additionally `minikube service <service-name>`. ***It will create a tunnel for our service to be accessible from the localhost.***
