## External (Cloud) Load Balancer vs Internal Reverse Proxy

External (Cloud) Load Balancer vs Internal Reverse Proxy
- https://aws.amazon.com/blogs/containers/exposing-kubernetes-applications-part-1-service-and-ingress-resources/
---
- External Load Balancer (`AWS Load Balancer Controller` as Ingress controller) [cloud managed service]
  - https://aws.amazon.com/blogs/containers/exposing-kubernetes-applications-part-2-aws-load-balancer-controller/
  - https://kubernetes-sigs.github.io/aws-load-balancer-controller/latest/
- Internal Reverse Proxy (`Nginx Ingress Controller` as Ingress controller)
  - https://aws.amazon.com/blogs/containers/exposing-kubernetes-applications-part-3-nginx-ingress-controller/
  - https://www.f5.com/products/nginx/nginx-ingress-controller

## Application Load Balancer (ALB) vs Network Load Balancer (NLB)

- https://aws.amazon.com/blogs/opensource/network-load-balancer-nginx-ingress-controller-eks/

- ALB works at 7 OSI layer
- NLB works at 4 OSI layer
