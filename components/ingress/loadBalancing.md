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
