## Elasticsearch Deployment

This solution provides a secure, production-grade deployment of Elasticsearch on Kubernetes using the official Elastic Helm Chart and automating TLS encryption with Cert-Manager. The configuration establishes a multi-role Elasticsearch cluster—with dedicated, isolated node pools for Master, Data, and Ingest nodes to ensure predictable performance, independent scalability and fault containment. The entire setup is automated via GitHub Actions for consistent CI/CD, prioritizing repeatability, security and modularity. The use of the official Helm chart ensures compatibility and a stable upgrade path while Cert-Manager's automatic certificate rotation eliminates the manual burden of managing TLS for end-to-end encryption.

## Deployment Workflow

### 1. Label the Nodes

```bash
kubectl label node <NodeName1> node-role.kubernetes.io/master=""
kubectl label node <NodeName2> node-role.kubernetes.io/data=""
kubectl label node <NodeName3> node-role.kubernetes.io/ingest=""
```

### 2. Install Cert-Manager

```bash
kubectl apply -f https://github.com/cert-manager/cert-manager/releases/download/v1.19.1/cert-manager.yaml
```

### 3. Create namespaces and certificates

```bash
kubectl apply -f namespace/
kubectl apply -f cert/
```

### 4. Add and update Helm repo

```bash
helm repo add elastic https://helm.elastic.co
helm repo update
```
### 5. Deploy Elasticsearch node roles

```bash
helm upgrade --install -n elasticsearch es-cluster-master elastic/elasticsearch -f values/master-values.yaml
helm upgrade --install -n elasticsearch es-cluster-data elastic/elasticsearch -f values/data-values.yaml
helm upgrade --install -n elasticsearch es-cluster-ingest elastic/elasticsearch -f values/ingest-values.yaml
```

