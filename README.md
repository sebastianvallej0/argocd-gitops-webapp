[README_argocd.md](https://github.com/user-attachments/files/35216534/README_argocd.md)
# argocd-gitops-webapp# argocd-gitops-webapp

Práctica individual de Procesos de Software (UDLA): despliegue de una aplicación web en Kubernetes con **Argo CD** (GitOps), usando este repositorio como fuente de verdad.

## Contenido

- `manifests/deployment.yml`: Deployment de nginx (2 réplicas, labels y selectors, requests y limits de CPU y memoria, puerto 80).
- `manifests/service.yml`: Service tipo NodePort (puerto 80, targetPort 80, nodePort 30080).
- `manifests/hpa.yml`: Horizontal Pod Autoscaler (mínimo 2, máximo 5 réplicas, objetivo de CPU 50%).
- `capturas/`: evidencia de la ejecución.

## Requisitos

- Docker Desktop, Minikube y kubectl
- Metrics Server activado en Minikube

## Cómo reproducirlo

```bash
# 1. Clúster y Metrics Server
minikube start --driver=docker
minikube addons enable metrics-server

# 2. Instalar Argo CD
kubectl create namespace argocd
kubectl apply -n argocd --server-side --force-conflicts -f https://raw.githubusercontent.com/argoproj/argo-cd/stable/manifests/install.yaml

# 3. Abrir la interfaz de Argo CD (https://localhost:8080, usuario admin)
kubectl port-forward svc/argocd-server -n argocd 8080:443
```

En Argo CD se crea la aplicación `webapp` con:

- Repository URL: `https://github.com/sebastianvallej0/argocd-gitops-webapp.git`
- Revision: `HEAD`
- Path: `manifests`
- Cluster: `https://kubernetes.default.svc`
- Namespace: `gitops-app`
- Sync Policy: Automatic (Prune Resources, Auto-Create Namespace)

## Verificación

```bash
kubectl get all -n gitops-app
kubectl get hpa -n gitops-app
minikube service webapp -n gitops-app
```

## Autor

Sebastián Vallejo
