# 🧩 Elasticsearch on Kubernetes — Production Helm Deployment

This repository provides a **secure**, **modular**, and **production-grade** setup for deploying **Elasticsearch** on **Kubernetes** using **Helm** and **Cert-Manager**, fully automated through **GitHub Actions**.

---

## 📘 Overview

This setup provisions a complete **Elasticsearch cluster** with dedicated **master**, **data**, and **ingest** nodes, all secured with TLS certificates and managed via CI/CD automation.

It’s designed for:

- **Repeatability** — consistent deployments across environments  
- **Scalability** — isolated roles and resource control  
- **Security** — full TLS encryption via Cert-Manager  

---

## 🚀 Key Features

- **Helm-based modular deployment** for master, data, and ingest node sets  
- **Automatic TLS certificate management** with Cert-Manager (CA + node certs)  
- **Continuous deployment** using GitHub Actions  
- **Secure kubeconfig management** via GitHub Secrets  
- **Idempotent operations** using `helm upgrade --install`  
- **Node Isolation** using Kubernetes `nodeSelector` labels  

---

## ⚙️ Prerequisites

Before running the workflow, ensure the following are in place:

- Kubernetes cluster **v1.24+**  
- **Helm 3.x** installed
- **Elastic** helm repo added (https://helm.elastic.co) 
- **Cert-Manager** installed  
- Labeled Kubernetes nodes (`master`, `data`, `ingest`)  
- GitHub repository with secret **`KUBECONFIG_DATA`** configured  

---

## 🧠 How to Deploy

Run the following commands to label your cluster nodes appropriately:

```bash
# 1. Label the Nodes
kubectl label node ip-10-0-1-131.eu-west-1.compute.internal node-role.kubernetes.io/master="" --overwrite=false
kubectl label node ip-10-0-2-99.eu-west-1.compute.internal node-role.kubernetes.io/data="" --overwrite=false
kubectl label node ip-10-0-3-128.eu-west-1.compute.internal node-role.kubernetes.io/ingest="" --overwrite=false

# 1. Install Cert-Manager
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.19.1/cert-manager.yaml

# 2. Create namespaces and certificates
kubectl apply -f namespace/
kubectl apply -f cert/

# 3. Add and update Helm repo
helm repo add elastic https://helm.elastic.co
helm repo update

# 4. Deploy Elasticsearch node roles
helm upgrade --install -n elasticsearch es-cluster-master elastic/elasticsearch -f values/master-values.yaml
helm upgrade --install -n elasticsearch es-cluster-data elastic/elasticsearch -f values/data-values.yaml
helm upgrade --install -n elasticsearch es-cluster-ingest elastic/elasticsearch -f values/ingest-values.yaml

