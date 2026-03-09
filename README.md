# cluster_practice
Your sentence has a small grammar mistake.
**Correct sentence:**
👉 *“Create a cluster and deploy KEDA in it. Share the document describing the procedure you followed. Configure observability for that cluster.”*

Now let me explain **what the task means** clearly.

---

# What This Task Means (Step-by-Step)

This looks like a **DevOps assignment or interview task**.
They want you to do **3 main things**:

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



Just tell me.
