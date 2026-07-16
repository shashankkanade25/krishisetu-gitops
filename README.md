# KrishiSetu — GitOps Deployment

<p align="center">
  <img alt="Kubernetes" src="https://img.shields.io/badge/Kubernetes-Manifest%20Driven-326CE5?logo=kubernetes&logoColor=white" />
  <img alt="Helm" src="https://img.shields.io/badge/Helm-Chart%20Based-0F1689?logo=helm&logoColor=white" />
  <img alt="GitOps" src="https://img.shields.io/badge/GitOps-Ready-10B981" />
</p>

Single source of truth for KrishiSetu Kubernetes deployments: raw manifests and a Helm chart for multi-environment rollouts.

---

## What’s here
- kubernetes/ — namespace, configmap, secret, deployment, service, ingress (production-oriented manifests)
- charts/krishisetu/ — Helm chart (templated, environment-friendly)

---

## Quick start

Deploy manifests (one-liner):
```bash
kubectl apply -f kubernetes/
```

Install via Helm:
```bash
helm upgrade --install krishisetu ./charts/krishisetu -n krishisetu --create-namespace
```

Verify:
```bash
kubectl get all -n krishisetu
kubectl describe ingress -n krishisetu
```

Notes:
- Ingress requires an NGINX Ingress Controller.
- Manifests use NodePort by default; Helm chart defaults to ClusterIP — adjust in values.yaml.

---

## Security (production)
- Do NOT commit plaintext secrets. Replace with SealedSecrets / ExternalSecrets / cloud secret manager.
- Enforce least-privilege service accounts and rotate credentials regularly.

---

## Recommended next steps
- Add Argo CD or Flux bootstrap for continuous reconciliation.
- CI: helm lint, helm template, kubeconform/kubeval, OPA/Conftest policies.
- Autoscaling (HPA), PodDisruptionBudget, NetworkPolicy.

---

## Maintainer
**Shashank Kanade** — https://github.com/shashankkanade25

---

## License
Add a LICENSE to define usage and contributions.
