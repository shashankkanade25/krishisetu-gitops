# 🌾 KrishiSetu GitOps Repository

<p align="center">
  <img alt="Kubernetes" src="https://img.shields.io/badge/Kubernetes-Manifest%20Driven-326CE5?logo=kubernetes&logoColor=white" />
  <img alt="Helm" src="https://img.shields.io/badge/Helm-Chart%20Based-0F1689?logo=helm&logoColor=white" />
  <img alt="GitOps" src="https://img.shields.io/badge/GitOps-Ready-10B981" />
  <img alt="Status" src="https://img.shields.io/badge/Deployment-Production%20Style-22C55E" />
</p>

## 📘 Overview

This repository contains the **deployment and infrastructure definitions** for the KrishiSetu application using a GitOps-friendly approach.

It includes:
- **Raw Kubernetes manifests** (`kubernetes/`) for direct cluster deployment.
- A **Helm chart** (`charts/krishisetu/`) for parameterized and reusable deployments.

In short, this repo is the **source of truth** for deploying KrishiSetu on Kubernetes.

---

## 🧱 Repository Structure

```text
krishisetu-gitops/
├── README.md
├── kubernetes/
│   ├── namespace.yaml
│   ├── configmap.yaml
│   ├── secret.yaml
│   ├── deployment.yaml
│   ├── service.yaml
│   └── ingress.yaml
└── charts/
    └── krishisetu/
        ├── Chart.yaml
        ├── values.yaml
        ├── .helmignore
        └── templates/
            ├── configmap.yaml
            ├── deployments.yaml
            ├── service.yaml
            └── ingress.yaml
```

---

## ⚙️ Deployment Assets Explained

### 1) `kubernetes/` — Raw Manifests

These files allow direct deployment with `kubectl`.

- **`namespace.yaml`**  
  Creates the `krishisetu` namespace.

- **`configmap.yaml`**  
  Defines non-sensitive app config:
  - `APP_NAME=KrishiSetu`
  - `ENV=production`

- **`secret.yaml`**  
  Defines sensitive runtime values (DB credentials, MongoDB URI) using `stringData`.

- **`deployment.yaml`**  
  Deploys the KrishiSetu workload with:
  - 2 replicas
  - Image: `shashankkanade25/krishisetu:v1`
  - Container port: `5000`
  - Resource requests/limits
  - Liveness & readiness probes (`/`)
  - Env vars sourced from ConfigMap and Secret

- **`service.yaml`**  
  Exposes the app internally/externally using:
  - Service type: `NodePort`
  - Port mapping: `80 -> 5000`
  - NodePort: `30080`

- **`ingress.yaml`**  
  Routes HTTP traffic via NGINX ingress class to `krishisetu-service` on port `80`.

---

### 2) `charts/krishisetu/` — Helm Chart

This chart provides a templated deployment model suitable for multi-environment configuration.

- **`Chart.yaml`**
  - Chart API: v2
  - Name: `krishisetu`
  - Type: `application`

- **`values.yaml`**
  Configurable runtime settings including:
  - Replica count (`replicaCount: 3`)
  - Container image repository/tag
  - Service type/port
  - Ingress toggle/class/host
  - Resource requests/limits

- **`templates/`**
  Contains chart templates for:
  - ConfigMap
  - Deployment
  - Service
  - Ingress

---

## 🚀 Quick Start

### Option A: Deploy raw manifests

```bash
kubectl apply -f kubernetes/namespace.yaml
kubectl apply -f kubernetes/configmap.yaml
kubectl apply -f kubernetes/secret.yaml
kubectl apply -f kubernetes/deployment.yaml
kubectl apply -f kubernetes/service.yaml
kubectl apply -f kubernetes/ingress.yaml
```

### Option B: Deploy with Helm

```bash
helm upgrade --install krishisetu ./charts/krishisetu -n krishisetu --create-namespace
```

---

## 🔍 Operational Notes

- Ensure your cluster has an **NGINX Ingress Controller** installed if using ingress.
- Verify DNS/host mappings for ingress host configuration (currently set to `local` in Helm values).
- Service type differs by approach:
  - Raw manifests: `NodePort`
  - Helm values default: `ClusterIP`

---

## 🔐 Security Recommendations

This repository currently defines application secrets in plain YAML via `stringData`. For production-grade security:

- Use **Sealed Secrets**, **External Secrets Operator**, or a cloud secrets manager.
- Avoid committing sensitive credentials and connection strings in Git.
- Rotate credentials and enforce least-privilege access.

---

## 🧪 Validation Commands

```bash
kubectl get ns
kubectl get all -n krishisetu
kubectl describe ingress -n krishisetu
kubectl logs deploy/krishisetu -n krishisetu
```

For Helm:

```bash
helm lint ./charts/krishisetu
helm template krishisetu ./charts/krishisetu -n krishisetu
```

---

## 🛣️ Suggested Next Improvements

- Add environment-specific Helm values (dev/stage/prod).
- Add Argo CD or Flux bootstrap manifests for full GitOps reconciliation.
- Add CI checks for:
  - `helm lint`
  - `kubeconform`/`kubeval`
  - policy checks (OPA/Conftest)
- Add Horizontal Pod Autoscaler (HPA).
- Add NetworkPolicy and PodDisruptionBudget.

---

## 👨‍💻 Maintainer

**Shashank Kanade**  
GitHub: [@shashankkanade25](https://github.com/shashankkanade25)

---

## 📄 License

No license file is currently present in this repository. Add a `LICENSE` file to clearly define usage terms.
