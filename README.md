# Elasticsearch on Kubernetes — Production Helm Deployment

This repository provides a **secure, modular, and production-ready** setup for deploying Elasticsearch on Kubernetes using **Helm** and **Cert-Manager**, fully automated through **GitHub Actions**.

---

## Overview

The goal of this project is to deploy a fully functional Elasticsearch cluster including **master**, **data**, and **ingest** nodes in a Kubernetes environment with automated certificate management and CI/CD.

All configurations are designed for **repeatability, scalability, and security**, making this setup suitable for both staging and production workloads.

---

## Key Features

- **Helm-based modular deployment** for master, data, and ingest nodes.
- **TLS encryption** via Cert-Manager (CA and node certificates).
- **GitHub Actions workflow** for continuous deployment.
- **Secure kubeconfig handling** through GitHub Secrets.
- **Idempotent Helm installs** (`helm upgrade --install`).
- **Node Role Isolation** (`nodeSelector`).

---

## Prerequisites

Before running the workflow, ensure the following:
- Label your nodes as master, data and ingest
- A Kubernetes cluster (v1.24 or newer)
- Helm 3.x
- Cert-Manager installed on the cluster
- A GitHub repository with the secret `KUBECONFIG_DATA` defined

## Actions
- kubectl label node ip-10-0-1-131.eu-west-1.compute.internal node-role.kubernetes.io/master="" --overwrite=false
- kubectl label node ip-10-0-2-99.eu-west-1.compute.internal node-role.kubernetes.io/data="" --overwrite=false
- kubectl label node ip-10-0-3-128.eu-west-1.compute.internal node-role.kubernetes.io/ingest="" --overwrite=false
- kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.19.1/cert-manager.yaml
- kubectl apply -f namespace/
- kubectl apply -f cert/

          

