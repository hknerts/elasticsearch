# Elasticsearch - Production Helm Deployment

This repository provides a **secure**, **modular** and **production-grade** setup for deploying **Elasticsearch** on **Kubernetes**, using the **official Elastic Helm Chart** and **Cert-Manager** for automated TLS management.  
Everything is fully automated via **GitHub Actions** for consistent, auditable CI/CD operations.

---

## Overview

This configuration provisions a **multi-role Elasticsearch cluster** with dedicated node pools for:

- **Master** nodes (cluster coordination)
- **Data** nodes (storage and indexing)
- **Ingest** nodes (pipeline and pre-processing)

Since there were only 3 nodes, i had to set the node roles one by one on the existing nodes. Normally, I would create a separate node group for each and manage their configurations differently.

Each role is **isolated at the node level** for predictable performance, fault containment and resource control.  
TLS certificates are managed automatically with **Cert-Manager**, ensuring end-to-end encryption inside the cluster.

The setup prioritizes:

- **Repeatability** — consistent across environments  
- **Scalability** — node roles scale independently  
- **Security** — TLS
- **Modularity** — separated Helm values per role  
- **Automation** — GitHub Actions handles deploys

---

## Why the Official Elastic Helm Chart?

The **official Elastic Helm chart** is chosen over community alternatives because it is:

- **Maintained by Elastic** — guaranteed compatibility with Elasticsearch versions  
- **Battle-tested** in production environments  
- **Feature-complete** — includes hooks, init containers, keystore injection, probes and configuration templating  
- **Upgradeable** — Helm releases support seamless rolling updates  

### ✅ Pros
- Officially supported by Elastic (no divergence risk)  
- Predictable upgrade path  
- Full configuration coverage (jvm, security, node roles, etc.)  
- Helm-native lifecycle management (`upgrade`, `rollback`, `template`)  

### ⚠️ Trade-offs
- Less flexibility compared to writing raw manifests  
- Larger learning curve for chart internals

---

## Why Cert-Manager?

**Cert-Manager** automates the issuance and renewal of TLS certificates for Elasticsearch and inter-node communication.

### ✅ Pros
- **Automatic rotation** of certificates (no downtime or manual renewal)  
- **Cluster-internal CA** management  
- **TLS** secure connection

### ⚠️ Trade-offs
- Adds a dependency (must maintain CRDs and controller)
- Can be overkill for simple, ephemeral test clusters
- Slight learning curve in managing issuers and cert resources  

**Bottom line:** In production, TLS automation is non-negotiable, Cert-Manager removes operational burden and eliminates human error.

---

## Why Label Nodes and Isolate Roles?

Elasticsearch is **heavily resource-bound** — CPU, memory and I/O characteristics vary per role.  
Mixing roles on the same node often leads to **contention**, **heap pressure** and **unpredictable latency**.

By labeling nodes and targeting deployments with `nodeSelector`, each Helm release runs only where it belongs:

| Role | Example Label | Purpose |
|------|----------------|----------|
| Master | `node-role.kubernetes.io/master` | Cluster coordination and quorum |
| Data | `node-role.kubernetes.io/data` | Indexing and storage |
| Ingest | `node-role.kubernetes.io/ingest` | Data transformation and pipelines |

### ✅ Advantages
- **Performance isolation** — no shared CPU/memory pressure  
- **Predictable scaling** — add capacity per role independently  
- **Fault containment** — node failures don’t impact all cluster functions  
- **Upgrade safety** — rolling updates affect isolated roles only  

### ⚠️ Trade-offs
- Requires **node pool management** (more infra coordination)  
- Slightly more complex Helm orchestration  
- Overhead in maintaining labels across environments  
