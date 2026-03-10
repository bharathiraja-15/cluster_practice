
1️⃣ Create a **Kubernetes Cluster** in **AWS or GCP**
2️⃣ Deploy **KEDA (Kubernetes Event Driven Autoscaler)**
3️⃣ Configure **Observability** for monitoring the cluster

Then **write a document explaining the steps you followed**.

---

# 1️⃣ Create a Kubernetes Cluster

You can choose either:

### Option 1 — AWS

Use **Amazon Web Services**

Create cluster using **Amazon EKS**

Example tools:

* Terraform
* eksctl
* AWS Console

Example architecture:

```
AWS
 ├── VPC
 │   ├── Public Subnet
 │   ├── Private Subnet
 │
 └── EKS Cluster
      ├── Worker Nodes
      ├── Pods
      └── Services
```

---

### Option 2 — GCP

Use **Google Cloud Platform**

Create cluster using **Google Kubernetes Engine**

---

# 2️⃣ Deploy KEDA

KEDA = **Kubernetes Event Driven Autoscaler**

It automatically **scales pods based on events**.

Example:

| Event                  | What Happens    |
| ---------------------- | --------------- |
| Kafka message increase | Pods scale up   |
| Queue backlog          | Pods scale up   |
| No messages            | Pods scale down |

Example use cases:

* RabbitMQ queue scaling
* Kafka consumer scaling
* Azure queue scaling
* Prometheus metrics scaling

Example architecture:

```
Application Pods
       │
       ▼
   Message Queue
       │
       ▼
      KEDA
       │
       ▼
 Auto scale pods
```

---

# 3️⃣ Configure Observability

Observability means **monitoring + logging + metrics**.

Common tools:

### Metrics

Use
Prometheus

Collects:

* CPU usage
* Memory usage
* Pod metrics
* Node metrics

---

### Dashboard

Use
Grafana

Shows dashboards like:

```
Cluster CPU
Pod Memory
Node Usage
Request Rate
Errors
```

---

### Logging

Use

* Fluentd
* ELK Stack

This collects logs from pods.

---

# Final Architecture They Expect

```
Cloud (AWS / GCP)

     Kubernetes Cluster
           │
           │
     ┌─────────────┐
     │ Application │
     └─────────────┘
           │
           ▼
         KEDA
           │
           ▼
     Auto Scaling Pods

Observability
     │
 ┌───────────────┐
 │ Prometheus    │
 │ Grafana       │
 │ Logging Stack │
 └───────────────┘
```

---

# 4️⃣ What Document They Want

You must submit a document like this:

```
1. Introduction
2. Architecture Diagram
3. Cluster Creation Steps
4. KEDA Installation
5. Application Deployment
6. Observability Setup
7. Screenshots
8. Conclusion
```




# EKS + KEDA + Observability Implementation Report

## 1. Project Title

**Implementation of Event-Driven Autoscaling in Amazon EKS using KEDA with Observability Stack (Prometheus & Grafana)**

---

# 2. Objective

The objective of this project is to:

* Provision a **Kubernetes cluster on AWS using EKS**
* Install **KEDA (Kubernetes Event Driven Autoscaling)**
* Deploy a **sample application**
* Implement **automatic pod scaling**
* Implement **monitoring and observability using Prometheus and Grafana**

This architecture demonstrates **modern DevOps practices for scalable microservices deployment**.

---

# 3. Architecture Overview

```
Terraform
   │
   ▼
AWS EKS Cluster
   │
   ▼
Worker Nodes
   │
   ├── Application Deployment
   ├── KEDA Autoscaler
   ├── Prometheus Monitoring
   └── Grafana Dashboard
```

Components used:

* AWS EKS
* Terraform
* Kubernetes
* KEDA
* Prometheus
* Grafana

---

# 4. Infrastructure Setup (Terraform)

Terraform was used to provision the infrastructure.

The following resources were created:

* AWS VPC
* Public and Private Subnets
* Internet Gateway
* EKS Cluster
* Managed Node Group
* IAM Roles

Terraform commands used:

```
terraform init
terraform plan
terraform apply
```

After infrastructure creation, cluster access was configured.

```
aws eks update-kubeconfig --region eu-north-1 --name keda-cluster
```

Cluster verification:

```
kubectl get nodes
```

---

# 5. Kubernetes Cluster Verification

After connecting to the cluster, system pods were verified.

```
kubectl get pods -A
```

Core Kubernetes components running:

* CoreDNS
* kube-proxy
* aws-node (CNI plugin)

This confirmed the **EKS cluster was operational**.

---

# 6. Installing KEDA

KEDA was installed using Helm.

Add Helm repository:

```
helm repo add kedacore https://kedacore.github.io/charts
helm repo update
```

Install KEDA:

```
helm install keda kedacore/keda \
--namespace keda \
--create-namespace
```

Verification:

```
kubectl get pods -n keda
```

KEDA components installed:

* keda-operator
* keda-metrics-apiserver

---

# 7. Deploying Sample Application

A sample **Nginx application** was deployed in Kubernetes.

Deployment configuration included **CPU resource requests and limits** to enable autoscaling.

Example deployment configuration:

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: keda-demo-app
  namespace: keda-demo
spec:
  replicas: 1
  selector:
    matchLabels:
      app: keda-demo
  template:
    metadata:
      labels:
        app: keda-demo
    spec:
      containers:
      - name: nginx
        image: nginx
        resources:
          requests:
            cpu: "100m"
          limits:
            cpu: "500m"
```

Deployment applied using:

```
kubectl apply -f deployment.yaml
```

---

# 8. Implementing KEDA Autoscaling

KEDA ScaledObject was created to enable autoscaling based on CPU utilization.

ScaledObject configuration:

```yaml
apiVersion: keda.sh/v1alpha1
kind: ScaledObject
metadata:
  name: nginx-scaledobject
  namespace: keda-demo
spec:
  scaleTargetRef:
    name: keda-demo-app
  minReplicaCount: 1
  maxReplicaCount: 5
  triggers:
  - type: cpu
    metadata:
      type: Utilization
      value: "50"
```

Apply configuration:

```
kubectl apply -f scaledobject.yaml
```

Verification:

```
kubectl get scaledobject -n keda-demo
```

KEDA automatically created an **HPA (Horizontal Pod Autoscaler)**.

```
kubectl get hpa -n keda-demo
```

---

# 9. Load Testing

Traffic was generated using a **busybox container** to simulate application load.

Command used:

```
kubectl run -i --tty load-generator \
--image=busybox \
--restart=Never \
-n keda-demo -- /bin/sh
```

Load generation script:

```
while true; do wget -q -O- http://keda-demo-app; done
```

This simulated **continuous application requests**.

---

# 10. Autoscaling Demonstration

During load generation:

```
kubectl get pods -n keda-demo -w
```

Observed behavior:

```
1 pod → 3 pods → 5 pods
```

KEDA automatically scaled the deployment based on CPU utilization.

When traffic stopped, pods scaled down automatically.

---

# 11. Observability Stack

Monitoring stack installed using **kube-prometheus-stack**.

Components installed:

* Prometheus
* Grafana
* Alertmanager
* Node Exporter
* kube-state-metrics

Installation command:

```
helm install monitoring prometheus-community/kube-prometheus-stack \
--namespace monitoring \
--create-namespace
```

Verification:

```
kubectl get pods -n monitoring
```

---

# 12. Grafana Dashboard

Grafana was used to visualize cluster metrics.

Metrics monitored:

* Node CPU usage
* Pod CPU usage
* Kubernetes cluster health
* Resource utilization

Grafana provides **real-time monitoring dashboards** for Kubernetes workloads.

---

# 13. Key Learnings

This project demonstrated:

* Infrastructure provisioning using **Terraform**
* Kubernetes cluster management in **AWS EKS**
* **Event-driven autoscaling using KEDA**
* Application deployment in Kubernetes
* Monitoring with **Prometheus**
* Visualization with **Grafana**

---

# 14. Conclusion

This implementation successfully demonstrated a **scalable Kubernetes architecture** with event-driven autoscaling and observability.

Using KEDA, applications can automatically scale based on workload demand, improving performance and resource utilization.

Prometheus and Grafana provide complete monitoring capabilities, enabling DevOps teams to observe and troubleshoot production environments effectively.

---

