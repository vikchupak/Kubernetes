## Using ingress requires 2 steps

1. Deploy an ingress controller
2. Deploy an ingress resource

## Ways to expose cluster

- Without Ingress controller, without Ingress resource
- Without Ingress controller, with Ingress resource **(WON'T WORK at all)**
- With Ingress controller, with Ingress resource
- With Ingress controller, without Ingress resource **(WON'T AFFECT LB creation, as if no Ingress controller deployed)**

---

- ALB is created for an Ingress resource with Ingress controller
- NLB is created for a service with `type NodePort / LoadBalancer`

## AWS explanation

- [Part1](https://aws.amazon.com/blogs/containers/exposing-kubernetes-applications-part-1-service-and-ingress-resources/)
- [Part2](https://aws.amazon.com/blogs/containers/exposing-kubernetes-applications-part-2-aws-load-balancer-controller/)
- [Part3](https://aws.amazon.com/blogs/containers/exposing-kubernetes-applications-part-3-nginx-ingress-controller/)

## `AWS Load Balancer Controller`

- With `AWS Load Balancer Controller`, traffic forwards to pods directly bypassing k8s `NodePort` and `ClusterIP` services.
  - It will privision "external" ALB for ingress resouces, and "external" NLB for service `type: NodePort / LoadBalancer` automatically.
  - Both ALB and NLB send traffic directly to pods via their private IPs, avoiding the extra hop through the Kubernetes `NodePort` and `ClusterIP` services.

![image](https://github.com/user-attachments/assets/541ce0a5-87f2-4ac7-8cec-2d32bb2aa535)

- Without `AWS Load Balancer Controller`, traffic will not be routed directly to pods. Instead, it will go through k8s NodePort using NLB.

## `Ingress-Nginx Controller` in AWS EKS

- Official doc https://kubernetes.github.io/ingress-nginx/deploy/

---

- `NGINX Ingress Controller` will NOT provision an "external" **ALB**. Instead, NGINX Ingress Controller is deployed as a service inside your cluster and needs to be exposed using `type LoadBalancer or NodePort`
  - AWS provisions "external" NLB to route traffic to the `NGINX Ingress Controller`
  - It acts as "internal" Layer 7 reverse proxy (internal ALB)
  - By default, it will forward traffic through k8s `NodePort / ClusterIP`services. But it can be configured to route traffic directly to pods via their private IPs.

![image](https://github.com/user-attachments/assets/74419c3b-d87b-45ee-8645-6a0992c890ae)

![image](https://github.com/user-attachments/assets/b2163f7f-3a6f-48a9-9475-83031961b0c2)

![image](https://github.com/user-attachments/assets/a7467612-9a2a-48a9-8b54-953377652023)

- nginx-ingress-controller deployed to aws as loadbalancer service type and aws provisions nbs to route trafic to it

![image](https://github.com/user-attachments/assets/06f070ce-c2b7-42e8-a5fa-aa49c73af81d)

## `Ingress-Nginx Controller` on-promises/bare-metal

- https://kubernetes.github.io/ingress-nginx/deploy/baremetal/
