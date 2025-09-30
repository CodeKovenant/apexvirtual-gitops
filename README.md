# ApexVirtual GitOps 🚀

Production-grade GitOps deployment patterns with **ArgoCD**, demonstrating multi-cluster Kubernetes management, sealed secrets, and declarative application orchestration.

## 📖 Overview
This repository showcases enterprise GitOps patterns using **ArgoCD ApplicationSets** to manage applications across multiple Kubernetes clusters.  
The implementation demonstrates automated application deployment, environment-specific configurations, and GitOps best practices for production infrastructure.

## ✨ Key Features

### 🌐 Multi-Cluster Management
- ApplicationSet generators for automated app deployment across environments  
- Separate production and development cluster configurations  
- App-of-apps pattern for managing ArgoCD itself  

### ⚙️ Configuration Management
- Kustomize base + overlay pattern for environment-specific configs  
- Native Helm chart integration via ArgoCD Applications  
- Structured approach to configuration reuse and environment isolation  

### 🔐 Security & Compliance
- Sealed Secrets for encrypted credentials in Git  
- Cert-Manager integration for automated TLS certificate management  
- Network policies and ingress security controls  

## 📂 Repository Structure
```
apexvirtual-gitops/
├── appsets/
│   ├── prod-cluster-appset.yml    # Production cluster ApplicationSet
│   └── dev-cluster-appset.yml     # Development cluster ApplicationSet
├── apps/
│   ├── vaultwarden/               # Secure credential management
│   │   ├── base/                  # Helm chart Application definition
│   │   └── envs/
│   │       └── prod/              # Production overlay with TLS, ingress
│   ├── traefik/                   # Ingress controller
│   │   ├── base/                  # Helm chart Application definition
│   │   └── envs/
│   │       ├── prod/              # Production configuration
│   │       └── dev/               # Development configuration
│   ├── cert-manager/              # Automated certificate management
│   ├── monitoring/                # Prometheus + Grafana observability stack
│   └── [additional apps]/         # Following the same pattern
└── root-app.yaml                  # App of apps entrypoint
```

## 🛠️ Technical Implementation

### 📦 ApplicationSet Pattern
The repository uses **Git directory generators** to automatically discover and deploy applications.  

- **Production ApplicationSet (`appsets/prod-cluster-appset.yml`)**:  
  - Scans for `apps/*/envs/prod` directories  
  - Deploys to the local cluster  
  - Creates Applications named `{app-name}-prod`  

- **Development ApplicationSet (`appsets/dev-cluster-appset.yml`)**:  
  - Scans for `apps/*/envs/dev` directories  
  - Deploys to remote dev cluster at `https://dev-cluster.lab.apexvirtual.internal:6443`  
  - Creates Applications named `{app-name}-dev`  

### 🧩 Base + Overlay Architecture
Each application follows a consistent structure:

```
apps/{app-name}/
├── base/
│   ├── {app-name}-app.yaml    # ArgoCD Application (for Helm charts)
│   └── kustomization.yaml
└── envs/
    ├── prod/
    │   ├── helm-values-patch.yaml
    │   ├── kustomization.yaml
    │   └── [additional resources]
    └── dev/
        ├── helm-values-patch.yaml
        ├── kustomization.yaml
        └── [additional resources]
```

The **base** defines the Application resource pointing to a Helm chart, while **environment overlays** provide values patches and additional resources like Certificates and IngressRoutes.

#### 🔑 Example: Vaultwarden Deployment
- **Base** (`apps/vaultwarden/base/vaultwarden-app.yaml`): Defines ArgoCD Application pointing to the Vaultwarden Helm chart  
- **Production Overlay (`apps/vaultwarden/envs/prod/`)**:  
  - Custom Helm values via strategic merge patch  
  - Certificate resource for TLS via cert-manager  
  - IngressRoutes for external and internal access  
  - Sealed secret for admin credentials  

## 📚 Application Catalog

### 🏗️ Core Infrastructure
- **Traefik**: Ingress controller with TLS termination and middleware  
- **Cert-Manager**: Automated Let's Encrypt certificate management  
- **Sealed Secrets**: Encrypted secrets for GitOps workflows  

### 📦 Applications
- **Vaultwarden**: Self-hosted password manager  
- **Monitoring Stack**: Prometheus operator, Grafana, node-exporter, kube-state-metrics  

### 💾 Storage & Platform
- **Longhorn**: Distributed block storage  
- **Portainer**: Container management interface  
- **Homepage**: Internal dashboard (manifests available upon request)  

## 🔄 Deployment Workflow
1. ApplicationSets continuously scan the Git repository  
2. New directories matching `apps/*/envs/{prod|dev}` are detected → Applications are created  
3. ArgoCD syncs Applications to their target clusters  
4. Kustomize merges base configurations with environment overlays  
5. Resources are applied with automated pruning and self-healing  

## 🛠️ Technology Stack
- **GitOps**: ArgoCD with ApplicationSets  
- **Configuration**: Kustomize with strategic merge patches  
- **Package Management**: Helm charts via ArgoCD integration  
- **Secrets**: Sealed Secrets (Bitnami)  
- **Ingress**: Traefik with custom middlewares  
- **Certificates**: Cert-Manager with Let's Encrypt  
- **Monitoring**: Prometheus Operator + Grafana  

## 🧭 Design Decisions
- **Why ApplicationSets?** → Scalable automation, no manual Application creation.  
- **Why Kustomize patches?** → Modify base Applications with environment-specific values while staying DRY.  
- **Why base + overlays?** → Maximize reuse, allow per-environment customization.  

---

## Portfolio Links

- **Infrastructure Provisioning**: [apexvirtual-terraform](https://github.com/fashomelab/apexvirtual-terraform) - Terraform IaC for multi-cluster Kubernetes
- **Configuration Management**: [apexvirtual-ansible](https://github.com/fashomelab/apexvirtual-ansible) - Ansible automation for bare metal and VMs
- **Main Platform Overview**: [corneb](https://github.com/fashomelab/corneb) - Complete ApexVirtual platform documentation

---

**Status**: Production-ready infrastructure patterns  
**Maintained**: Active development

*GitOps deployment architecture for the ApexVirtual platform - showcasing declarative Kubernetes management at scale.*
