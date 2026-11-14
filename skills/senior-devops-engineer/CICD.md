# CI/CD

Consult this when designing, writing or fixing pipelines. CI/CD is the core of the role, so every pipeline is treated as production code.

## Choosing the platform

Make this choice with the user (SKILL step 2) and record it in an ADR.

| Platform | Fits when | Watch out |
|---|---|---|
| **GitHub Actions** | The code is on GitHub. Fast setup, a large ecosystem of actions, OIDC to clouds, environments with approvals. The default. | Third-party actions are part of your supply chain, so pin them by SHA. Minutes cost money on private repos. |
| **Jenkins** | Self-hosted or on-prem requirements, complex or long-running builds, heavy existing investment in Jenkins, an air-gapped network | You must operate and patch it. Use Pipeline-as-code and shared libraries, never jobs configured by hand. |
| **GitLab CI** | The code is on GitLab, or an all-in-one platform is wanted (registry, security scans) | Self-managed GitLab needs operating. |
| Others (Argo Workflows, Tekton, Buildkite, CircleCI) | A specific need, such as Kubernetes-native or very large-scale builds | Present it only when such a need exists. |

## Pipeline design (any platform)

```
PR / push:  lint → type-check → unit tests → build → integration tests → security scans → (package)
main:       … same … → build & push image (SHA tag) → deploy to staging → smoke tests
release:    tag v* → promote the SAME artifact → manual approval → deploy prod → smoke → (auto-rollback on failure)
```

- **Fail fast.** The cheapest checks run first. Independent jobs run in parallel.
- **Build once, promote everywhere.** Artifacts and images are immutable and identified by SHA.
- **Caching.** Cache dependencies by lockfile hash. Cache Docker layers with the `type=gha` or registry cache.
- **One source of truth for versions.** Derive the version from the git tag; use semver plus conventional commits for changelogs where the user wants them.
- **Trunk-based flow.** Merge through PRs only, and branch protection requires green checks.
- **Secrets.** Inject secrets from the platform's secret store or an external vault, scoped to an environment, and mask them in logs. Never `echo` them, and never pass them to untrusted PR code. Workflows triggered by forks get no secrets.
- **Supply-chain security.**
  - Scan dependencies, images, IaC and secrets in every run: `npm audit` or `pip-audit`, Trivy, Checkov, gitleaks.
  - Generate an SBOM (Syft).
  - Sign images (cosign).
  - Keep dependency updates automatic with Dependabot or Renovate.
- **Deployment strategies.** Rolling is the default. Use blue-green or canary when downtime or risk is unacceptable. Every deploy has a health-gated rollout and a scripted rollback.
- **Notifications.** Report failures to the people who own the change (a PR status, chat), and keep the noise down.

## GitHub Actions specifics

- Set `permissions:` at the top of each workflow to the minimum it needs (for example `contents: read`), and widen it per job only where required.
- Pin third-party actions to a full commit SHA, with a version comment beside it: `uses: actions/checkout@<sha> # v4`.
- Set `concurrency:` per ref with `cancel-in-progress: true` for PRs.
- Share logic through reusable workflows (`workflow_call`) and composite actions. Use matrices for versions and platforms.
- Use `environment:` with required reviewers for production, and OIDC (`id-token: write`) for cloud credentials.
- Set `timeout-minutes` on every job.
- Lint workflows with `actionlint`, and run them locally with `act` where feasible.

## Jenkins specifics

- Use a declarative `Jenkinsfile` in the repo. Put shared steps in a versioned **shared library**.
- Run agents as ephemeral containers or Kubernetes pods (the Docker or Kubernetes plugin), not long-lived snowflake agents.
- Use `options { timeout(...); disableConcurrentBuilds(); buildDiscarder(...) }`.
- Take credentials only from the Jenkins credentials store or Vault through `withCredentials`. Never put them in parameters.
- Manage Jenkins configuration as code: the Configuration as Code (JCasC) plugin, and plugins pinned in a `plugins.txt`. Keep the plugin count small and plugins updated.
- Validate the Jenkinsfile with the declarative linter before handing off.
