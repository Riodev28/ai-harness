# Platform tools

Consult this when the product would benefit from a supporting DevOps system. Every new system is something the user has to run, pay for and maintain, so treat each as a **recommendation to the user, never a silent addition**.

## How to recommend

1. **Name the concrete problem** in this product. For example: "secrets are copied into three `.env` files by hand", or "images are pushed to Docker Hub with no scanning".
2. **Offer 2–3 options,** including the simplest or managed one and "nothing yet". For each, give its operating cost (self-hosted or managed, money, upkeep) and the effort to adopt it.
3. **Recommend one** and say what would make you switch.
4. **Wait for the user's decision,** then record it as an ADR. Adopt tools incrementally.

Match the size of the tool to the product. A personal project rarely needs to self-host Vault, Harbor or Kubernetes, but a team platform with many services often does.

## Catalog

| Need | Options | Recommend when |
|---|---|---|
| **Container registry** | **Harbor** (self-hosted: scanning, signing, replication, RBAC, proxy cache), GHCR, GitLab Registry, ECR/GCR/ACR, Docker Hub | **Harbor** for on-prem, many teams, policy or scanning needs, or air-gapped setups. **GHCR** for GitHub-hosted projects that need no extra infrastructure. **The cloud's own registry** when deploying to that cloud |
| **Secrets management** | **HashiCorp Vault** (or OpenBao), cloud secret managers (AWS Secrets Manager, GCP Secret Manager, Azure Key Vault), Doppler, Infisical, SOPS + age, External Secrets Operator (K8s) | **Vault** for dynamic credentials, multi-cloud or on-prem, and many services. **A cloud secret manager** when the product lives on one cloud. **SOPS** for small projects that want encrypted secrets in git. **CI secret stores** alone for one app with few secrets |
| **Infrastructure as code** | Terraform / OpenTofu, Pulumi, Ansible (config management), CloudFormation/Bicep | **Terraform or OpenTofu** with remote state and locking as the default. **Ansible** for configuring VMs. **Pulumi** when the team prefers a general-purpose language |
| **Orchestration / runtime** | Docker Compose on a VPS, k3s, managed Kubernetes (EKS/GKE/AKS), Nomad, PaaS (Fly.io, Render, Railway, Cloud Run, App Runner) | **Compose on a VPS or a PaaS** for small products. **Kubernetes** only when there are many services, autoscaling or multi-team needs, and someone to operate it |
| **GitOps / CD** | Argo CD, Flux | Kubernetes deployments that need a declarative, auditable rollout and drift detection |
| **Packaging for K8s** | Helm, Kustomize | **Helm** for distributable charts. **Kustomize** for per-environment overlays of your own manifests |
| **Observability** | Prometheus + Grafana, Loki, Tempo, OpenTelemetry Collector, Grafana Cloud, Datadog, Sentry (errors) | **OpenTelemetry** for instrumenting the app. **Grafana Cloud or Sentry** for small teams. **A self-hosted Prometheus/Grafana/Loki stack** when on-prem |
| **Artifact repository** | Nexus, Artifactory, GitHub Packages | When private packages (npm, Maven, PyPI) or a proxy cache of upstream registries are needed |
| **Code quality / security** | SonarQube or SonarCloud, Trivy, Snyk, Dependabot, Renovate, gitleaks | **Trivy, Renovate and gitleaks** as a free baseline. **SonarQube** when the team wants quality gates tracked across many repos |
| **Feature flags** | Unleash, Flagsmith, LaunchDarkly, OpenFeature SDK | For trunk-based development with gradual rollouts |
| **Edge, ingress, TLS** | Traefik, Caddy, NGINX, cert-manager, Cloudflare | **Caddy or Traefik** for automatic TLS on a VPS. **cert-manager** on Kubernetes |
