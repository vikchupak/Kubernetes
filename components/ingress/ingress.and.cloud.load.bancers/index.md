#  Linode Kubernetes Engine (LKE)

- When you create a Kubernetes Service of type `LoadBalancer`, Linode provisions a Linode's `NodeBalancer` for your cluster.
- The NodeBalancer automatically receives an external IP (public IP address) from Linode, which acts as the entry point for external traffic to your Kubernetes cluster.
- The external IP of the LoadBalancer service will be mapped to the NodeBalancer that Linode provisions.
- **In essence, Kubernetes does not directly assign an IP to the LoadBalancer service itself. Rather, the NodeBalancer is given the IP, and then it forwards traffic to the Ingress Controller running inside your Kubernetes cluster.**
- The NodeBalancer is acting as a reverse proxy to route external traffic to the k8s LoadBalancer Service.
- **From an external perspective, you will use the same IP address for both your NodeBalancer and  the k8s LoadBalancer Service because Linode's infrastructure binds the NodeBalancer to that IP address.**
- If you have multiple services of type LoadBalancer in Kubernetes (e.g., several services needing external access), Linode may provision multiple NodeBalancers, each with its own external IP

The same IP for **cloud Load balancer** and **k8s load balancer service**
![image](https://github.com/user-attachments/assets/4be2908a-1bb8-4332-b2e1-81bfc13abc99)
![image](https://github.com/user-attachments/assets/15ee293d-79c9-4dbd-b1de-6f3e8d493c0c)
