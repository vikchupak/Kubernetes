## External (Cloud) Load Balancer vs Internal Reverse Proxy

External (Cloud) Load Balancer vs Internal Reverse Proxy
- https://aws.amazon.com/blogs/containers/exposing-kubernetes-applications-part-1-service-and-ingress-resources/
---
- External Load Balancer (`AWS Load Balancer Controller` as Ingress controller) [cloud managed service]
  - Helps to manage `AWS Elastic Load Balancers` (ELBs) for Kubernetes
  - It integrates with the Kubernetes API to provision and manage Application Load Balancers (ALBs) and Network Load Balancers (NLBs) for services within a Kubernetes cluster
  - The AWS Load Balancer Controller acts as the Ingress controller for AWS
  - When you create a Kubernetes Ingress resource, the AWS Load Balancer Controller automatically provisions an ALB or NLB to handle traffic based on the Ingress rules.
  - https://aws.amazon.com/blogs/containers/exposing-kubernetes-applications-part-2-aws-load-balancer-controller/
  - https://kubernetes-sigs.github.io/aws-load-balancer-controller/latest/
- Internal Reverse Proxy (`Nginx Ingress Controller` as Ingress controller)
  - https://aws.amazon.com/blogs/containers/exposing-kubernetes-applications-part-3-nginx-ingress-controller/
  - https://www.f5.com/products/nginx/nginx-ingress-controller
 
### Use Both Controllers Together
In some cases, you might want both:
- Use `AWS Load Balancer Controller` for external traffic and expose services to the internet.
- Use `NGINX Ingress Controller` for internal routing or more advanced traffic management within the cluster.

## Application Load Balancer (ALB) vs Network Load Balancer (NLB)

- https://aws.amazon.com/blogs/opensource/network-load-balancer-nginx-ingress-controller-eks/

- ALB works at 7 OSI layer
- NLB works at 4 OSI layer
