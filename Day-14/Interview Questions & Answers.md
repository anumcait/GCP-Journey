# GKE Interview Q&A

## 1. What is GKE?
Google Kubernetes Engine (GKE) is a managed Kubernetes service from Google Cloud that automates cluster management, scaling, patching, and upgrades.

## 2. Why use GKE over self-hosted Kubernetes?
- Reduces operational overhead  
- Auto-scaling for workloads  
- Automatic upgrades and patches  
- VM optimization and cost efficiency  

## 3. Explain Kubernetes architecture briefly.
- **Control Plane:** Manages cluster decisions (API server, Scheduler, Controller Manager, etcd)  
- **Worker Nodes:** Run Pods and containers  
- **Pods:** Smallest deployable units  
- **ReplicaSets:** Ensure desired pod count  
- **Ingress/Services:** Load balancing and routing  

## 4. What are the modes of GKE?
- **Standard Mode:** Manages control plane  
- **Autopilot Mode:** Manages both control plane and data plane  

## 5. How does Kubernetes handle ephemeral containers?
- Pods ensure containers restart automatically  
- ReplicaSets maintain the desired number of pods  

## 6. What are the challenges of self-hosted Kubernetes?
- Complex setup and upgrades  
- Cost inefficiency due to unused resources  
- Requires deep Kubernetes expertise  

## 7. What is a pod in Kubernetes?
- A pod is a group of one or more containers with shared storage/network and a specification for running containers.

## 8. How do you scale applications in GKE?
- Use **Horizontal Pod Autoscaler (HPA)**  
- Use **Cluster Autoscaler** for scaling node pools automatically  

## 9. How does GKE reduce costs?
- Auto-scales worker nodes  
- Optimizes VM types based on workload  
- Removes unused resources in Autopilot mode  

## 10. Can you run enterprise applications on GKE?
Yes, you can deploy multi-microservice applications with high availability, security, and scalability.

