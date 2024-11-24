## Cluster on On-promises
- **Spin up**
  - Create a cluster from a scratch. We need to create and manage everithing yourselves
    - Create and manage **master** and worker nodes.
- **Data persistance**
  - We need to create physical storage and make it available for the cluster
    - Create Persistent Volume
    - Attach the volumes to pods
- **Load balancing**
  - We need to install and run Ingress Controller to manage routing of incomming requests
    - Configure your routing rules
    - We can set up Ingress Controller as Load Balancer
      - Nginx inside controller can be used as load bancer

## Cluster on Cloud using Managed service
- **Spin up**
  - Most setup is automated and done by cloud provider
  - We select number of worker nodes and resouces on them with everything pre-installed
  - Master nodes are managed and created by cloud provider. So we only pay for worker nodes.
  - Select region/data center to decrease network latency
- **Data persistance**
  - We can use `Storage Class` to dynamicly provide storage by cloud provider
- **Load balancing**
  - Cloud providers provide their own Load Balancer implementations
    - **It stands in front of the Ingress controller**. In this scenario Ingress controller works as a "router".
    - The cloud load balancer routes external traffic to the Ingress Controller.
  - We can configure a session stickiness
  - We can set SSL/TLS Certification
- **Migration to another cloud**
  - Not easy at all as cloud providers provide a lof of cloud-specific features, though very similar.
    - Your apps get highly tied to a cloud provider
    - Re-programming and reconfiguring necessary
- **Automation**
  - Cloud providers provide access to their resouces via automation tools (Terraform, Ansible), which allows to automate your infrastructure/devops work

## Architecture of cloud provider load balancer over ingress controller

The architecture of a **cloud provider load balancer** integrated with an **Ingress Controller** typically involves an external load balancer provided by the cloud (e.g., AWS ELB, GCP Cloud Load Balancing, Azure Load Balancer) that sits at the edge of the Kubernetes cluster. It serves as the entry point for external traffic and forwards that traffic to the Kubernetes cluster’s **Ingress Controller**. Let’s break down how the cloud provider's load balancer works with the **Ingress Controller** in a typical setup.

### **Overview of the Architecture:**
1. **External Traffic**: Incoming HTTP(S) traffic from clients (e.g., browsers, APIs) enters the cloud provider’s network.
2. **Cloud Load Balancer**: The cloud provider’s **load balancer** (such as AWS ELB, GCP’s HTTP(S) Load Balancer, or Azure’s Application Gateway) receives and distributes the traffic across the available **Ingress Controller services** running in the Kubernetes cluster.
3. **Ingress Controller**: The **Ingress Controller** inside the Kubernetes cluster is responsible for routing the traffic to the correct service, typically based on domain name or URL path, according to the rules specified in the **Ingress resources**.
4. **Kubernetes Service**: The **Kubernetes Service** for the backend application handles load balancing between the **pods** running the application.

### **Detailed Flow:**

#### 1. **Cloud Load Balancer**
   - The **cloud load balancer** is the first point of contact for incoming traffic from external clients.
   - It listens on HTTP(S) ports (80/443) and, based on the incoming **domain name** (e.g., `nginx.example.com` or `traefik.example.com`), forwards traffic to the **Ingress Controller service** in the Kubernetes cluster.
   - The load balancer distributes traffic across multiple **Ingress Controllers** for **high availability** and **scalability**. If you have multiple **Ingress Controller services** deployed across multiple nodes in the Kubernetes cluster, the cloud load balancer ensures the traffic is evenly spread.

#### 2. **Ingress Controller Service**
   - The **Ingress Controller service** (Nginx, Traefik, or others) is exposed as a **Kubernetes service** (often a **LoadBalancer service** or **NodePort** in some cases).
   - Important below
   - ***!!!*** The **cloud load balancer** forwards traffic to the **Ingress Controller service!!!** using the IP address of the Kubernetes **service**.
   - Important above
   - If multiple **Ingress Controller pods** are running (for scalability and availability), the **service** automatically load balances traffic to them.
   - The **Ingress Controller** is responsible for interpreting the **Ingress resources** and routing the traffic to the correct backend service based on the **host** and **path** defined in the **Ingress resource**.

#### 3. **Ingress Controller**
   - The **Ingress Controller** (such as Nginx or Traefik) inspects the incoming request and determines how to route the request to the appropriate **backend service** based on the **Ingress resource** configuration.
   - For example, it might forward requests for `nginx.example.com` to a service called `nginx-service` or `traefik.example.com` to `traefik-service`.
   - The **Ingress Controller** handles the traffic routing logic and implements features like SSL termination, rate limiting, authentication, etc.
   
#### 4. **Kubernetes Service**
   - After the **Ingress Controller** forwards the traffic to the appropriate backend service, the **Kubernetes Service** within the cluster takes over.
   - The **Kubernetes Service** is responsible for load balancing the request to the **pods** running the application. It can use methods like **Round Robin** or **Least Connections** to distribute the traffic evenly across the available pods.
   - The **Kubernetes Service** exposes the backend application’s pods to the Ingress Controller, and it ensures traffic is balanced across the pods for scalability.

### **Cloud Provider Load Balancer vs. Ingress Controller**

While both the **cloud load balancer** and **Ingress Controller** can distribute traffic, their roles are different:

- **Cloud Load Balancer**:
  - Acts as the **entry point** for all external HTTP(S) traffic.
  - Distributes traffic across **Ingress Controllers** based on domains or paths.
  - Provides **scalability** and **fault tolerance** for the **Ingress Controller**.

- **Ingress Controller**:
  - Routes traffic within the cluster to the appropriate **backend service** based on the rules in the **Ingress resources**.
  - May offer additional features like **SSL termination**, **rate limiting**, and **security policies**.
  - Ensures that traffic gets routed to the correct **service**, which in turn directs it to the **pods**.

### **Architecture Example**:

Here’s a simple diagram of how this works:

```
         +-------------------+    
         |    External       |    
         |    Clients        |    
         +--------+----------+    
                  |               
         +--------v----------+    
         | Cloud Load        |    
         | Balancer (e.g.,    |    
         | AWS ELB, GCP LB)   |    
         +--------+----------+    
                  |               
         +--------v----------+    
         | Ingress Controller |    
         | Service (Nginx)    |    
         +--------+----------+    
                  |               
         +--------v----------+    
         | Ingress Controller |    
         | Pod (Nginx Pod 1)  |    
         +--------+----------+    
                  |               
         +--------v----------+    
         | Kubernetes Service |    
         | (payment-service)    |    
         +--------+----------+    
                  |               
         +--------v----------+    
         | Application Pods  |    
         | (Pod 1, Pod 2)     |    
         +-------------------+
```

### **Step-by-Step Traffic Flow in This Setup:**
1. **External Clients** (users, API calls) send HTTP(S) traffic to `nginx.example.com`.
2. The **cloud load balancer** (e.g., AWS ELB) receives this traffic and forwards it to the **Ingress Controller service** (e.g., Nginx service) based on the domain name.
3. The **Ingress Controller** (running in multiple pods) processes the request and forwards it to the appropriate **Kubernetes service** (e.g., `nginx-service`).
4. The **Kubernetes service** load-balances the request across the **application pods** (e.g., Pod 1, Pod 2) running the backend application.
5. The backend pods process the request, and the response is sent back through the same path.

### **Key Points:**

- **Cloud Load Balancer**: Routes external traffic to the **Ingress Controller service**. It ensures that traffic is distributed evenly across multiple **Ingress Controllers** (if there are multiple).
- **Ingress Controller**: Routes the traffic to the appropriate **Kubernetes service** based on the Ingress resource rules (host/path).
- **Kubernetes Service**: Balances the traffic between the available **pods** running the application.

### **Summary:**
The **cloud provider load balancer** distributes incoming traffic across the **Ingress Controller services**, ensuring that no single Ingress Controller is overwhelmed. The **Ingress Controller** is responsible for routing traffic to the appropriate **backend services**, and **Kubernetes Services** handle load balancing between the application pods.

This architecture ensures that you have scalability, high availability, and fault tolerance at each layer of the system, from the external load balancing to the internal load balancing between the pods.

Let me know if you need more details or clarification!

