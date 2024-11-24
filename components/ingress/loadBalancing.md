## Ingress Controller is NOT a LOAD BALANCER, but a "ROUTER"

### **The Role of Ingress Controller**
- The **Ingress Controller** is responsible for routing **external HTTP(S)** traffic to the correct **Kubernetes services** based on **Ingress rules**.
- It operates at **Layer 7 (Application Layer)** and typically handles:
  - **Host-based routing** (e.g., `api.example.com` vs. `www.example.com`).
  - **Path-based routing** (e.g., `/api` vs. `/web`).

It routes traffic to the **Kubernetes Service** (a layer 4 load balancer) that is associated with the corresponding set of pods.
  
However, **external traffic** still needs to reach the **Ingress Controller** in the first place. This is where you might need:
1. **Load Balancer** (like MetalLB or a cloud-native load balancer): To handle traffic **outside the cluster** and route it to the Ingress Controller.
2. **DNS Configuration**: To point external users to the correct public IP where the Ingress Controller is accessible.

### **Summary**
1. **External Traffic** → **Ingress Controller** (Layer 7) → **Kubernetes Service** (Layer 4) → **Pods**
   - **Ingress Controller**: Routes traffic to services (Layer 7 routing). Decides **which service** (or backend) should handle the incoming traffic based on its rules (path, hostname).
   - **Kubernetes Service**: Balances traffic across pods (Layer 4 load balancing). Actually balances the traffic **between the pods** for that service.

## External Load Balancer balances traffic to the Ingres controller(s)

#### **1. External Load Balancer:**
- **Purpose**: Its primary role is to distribute incoming **external traffic** (from the outside world) to one or more **Ingress Controllers**. If you only have one Ingress Controller, it essentially **proxies** traffic to it.
- **What It Balances**: The external load balancer **balances traffic** across **Ingress Controller instances**, not directly to the pods themselves. For example:
  - If you have **multiple ingress controllers** deployed across different nodes, the external load balancer ensures that traffic is **distributed evenly** across these controllers.
  - If you only have **one Ingress Controller**, it simply directs all the traffic to it (this is still considered load balancing, but with a single target).

#### **Why It’s Called a Load Balancer:**
- The term **load balancer** is used because it is **balancing** the traffic to the **Ingress Controller** instances in the Kubernetes cluster. This is an important role for ensuring that external traffic can efficiently reach the cluster's entry point (the Ingress Controller).
- In cloud environments (e.g., AWS ELB, GCP Load Balancer), the cloud-managed load balancer typically distributes traffic to **Ingress Controller pods** running in different availability zones or regions. In on-premises setups, a tool like **MetalLB** serves this function.

#### **2. Ingress Controller:**
- **Purpose**: Once the external traffic reaches the **Ingress Controller**, it takes over and **routes traffic based on rules** (host, path, etc.) to the correct **Kubernetes Service**.
- **What It Balances**: The Ingress Controller itself does **Layer 7 (HTTP/HTTPS) routing** to different services within the cluster. The actual **load balancing** of traffic to **pods** is handled by the **Kubernetes Service**.

#### **3. Kubernetes Service:**
- The **Kubernetes Service** is the component that actually **balances traffic** across the **pods** of a service, distributing it based on **IPtables** or **IPVS** load balancing mechanisms (Layer 4).

---

### **Illustrating the Flow of Traffic:**
Let’s walk through a scenario to visualize the flow of traffic:

1. **External Request**:  
   A user makes an HTTP request to `http://example.com/api`.
   
2. **External Load Balancer**:  
   - The external load balancer (like MetalLB or a cloud LB) receives the request.
   - If you have **multiple Ingress Controllers**, it will distribute the traffic across them (this is **balancing**).
   - If you only have **one Ingress Controller**, it forwards all traffic to it (still **considered load balancing** to the Ingress Controller).

3. **Ingress Controller**:  
   - The Ingress Controller looks at the request and determines which **Kubernetes Service** should handle it based on the **Ingress rules**.
   
4. **Kubernetes Service**:  
   - The selected **Kubernetes Service** load balances the traffic to one of its **pods**.

---

### **Why Use a Load Balancer for the Ingress Controller?**
Here’s why we still need an **external load balancer**:
1. **High Availability**:  
   - If you have **multiple Ingress Controllers** (for high availability), the external load balancer ensures that traffic is evenly distributed across them. Without it, traffic would only go to one Ingress Controller.
   
2. **Scalability**:  
   - As traffic grows, you can scale your Ingress Controllers. The external load balancer ensures that traffic is efficiently routed to the available controllers.

3. **Separation of Concerns**:  
   - The external load balancer handles **traffic distribution to the entry point (Ingress Controller)**, while the Ingress Controller handles **routing** to the correct services.

### **Summary:**
- The **external load balancer** balances traffic **to the Ingress Controller** (whether you have one or multiple controllers).
- The **Ingress Controller** then routes traffic to the correct **Kubernetes Service**.
- The **Kubernetes Service** load balances the traffic **to the pods**.

Even if you're using only one **Ingress Controller**, the external load balancer is still fulfilling its role of ensuring that traffic **reaches** that entry point in a controlled manner, hence the term "load balancer."

## Multiple Ingress controllers

Kubernetes allows you to run **multiple Ingress Controllers** in the same cluster. In fact, this is a common approach in production environments where high availability and load balancing are required.

Having multiple Ingress Controllers can help with:
- **High availability**: If one Ingress Controller instance fails, others can still handle the traffic.
- **Fault tolerance**: If one node goes down, the traffic can be rerouted to other nodes that have the Ingress Controllers running.
- **Load balancing**: With multiple Ingress Controllers running, an external load balancer can distribute traffic between them, improving resource utilization and ensuring there is no single point of failure.

### **2. In general, Ingress Controllers are NOT Bound to Specific Nodes**

But we can bound them across different nodes for high availability and scalability.

### **3. Are Multiple Ingress Controllers Just Instances of the Same Controller or Completely Different?**
- **Multiple Instances of the Same Controller**: In most cases, when we refer to **multiple Ingress Controllers**, we are talking about running **multiple instances of the same Ingress Controller** (e.g., Nginx Ingress Controller). These instances are typically configured in a way that they can all work together to handle incoming traffic.
   - Multiple instances of the same Ingress Controller refer to **multiple pods running the same Ingress Controller**, but they typically work together as a **single logical service**.
  - For example, you may have two or more instances(pods) of the **Nginx Ingress Controller** running on different nodes for load balancing and high availability. These instances can share the same configuration and work in a coordinated manner.
  - The **Ingress Controller** pods are often **stateless**, meaning you can have multiple instances with the same configuration, and they will all be able to process requests as long as they share access to the required resources (like ingress rules, config maps, etc.).

- **Completely Different Controllers**: You can also run **completely different Ingress Controllers** in the same cluster. For example, you might run both the **Nginx Ingress Controller** and the **Traefik Ingress Controller** side-by-side. This could be done for various reasons, such as:
  - **Different use cases**: You may want Nginx for certain types of traffic (e.g., simple HTTP/HTTPS) and Traefik for others (e.g., for advanced routing, WebSocket support).
  - **Compatibility with specific features**: Different Ingress Controllers may support specific features that are better suited for certain applications.

  In this case, each **Ingress Controller** will have its own set of resources (e.g., ingress resources, configurations), and you can configure different routes to be handled by different controllers using annotations or namespaces.

---

### **How Traffic is Handled with Multiple Ingress Controllers:**
When you have multiple Ingress Controllers in your cluster, the **external load balancer** (e.g., MetalLB, Cloud LB, etc.) will balance the traffic between the Ingress Controller instances. 

- If you have **multiple Ingress Controllers of the same type**, they are typically **stateless**, so traffic is routed to the next available instance.
- If you have **multiple types of Ingress Controllers** (e.g., Nginx and Traefik), you can use annotations or separate ingress resources to direct traffic to specific controllers.

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

