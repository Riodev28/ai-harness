# Containers

Consult this when deciding whether to dockerize a product, and when writing Dockerfiles or Compose files.

## When to dockerize

**Dockerize by default** when the product has any of the following:
- several runtime dependencies or services (database, cache, queue, workers);
- system libraries or a toolchain that is hard to install;
- more than one environment that must behave the same (dev, CI, staging, prod);
- a server deployment target.

**Skip containerizing the app itself** when its artifact runs somewhere else:
- **Mobile apps** (an APK or IPA): containerize the *build environment* only if it is complex.
- **Static sites** deployed to a CDN or object storage.
- **Libraries** published to a package registry.
- **Serverless functions,** unless they are deployed as container images.

When the decision is unclear, ask the user, giving the trade-off between setup cost and environment parity.

## Dockerfile rules

- **Use multi-stage builds.** A `build` stage holds the toolchain and dev dependencies. The `runtime` stage copies only the artifacts.
- **Use a minimal, pinned runtime base:** distroless, `-slim`, Alpine (only when musl is fine for the app), or Chainguard. Pin the tag, and the digest for production: `FROM node:22.11-slim@sha256:…`.
- **Order layers for the cache.** Copy the manifests and lockfiles, install dependencies, then copy the source. Use BuildKit cache mounts for package managers (`RUN --mount=type=cache,target=/root/.npm npm ci`).
- **Ship a small image.** Keep a `.dockerignore` (excluding `.git`, `node_modules`, build output, `.env*` and tests). Clean package caches in the same layer that created them. Install production dependencies only.
- **Run as non-root.** Add `USER app` (UID ≥ 10000), make the filesystem read-only where possible, and drop capabilities at runtime.
- **Supply secrets correctly.** Build-time secrets come in through `RUN --mount=type=secret` and runtime secrets through the environment or mounted files from a secrets manager. Never use `ARG` or `ENV` for them.
- **Use the exec form** for the entrypoint, `ENTRYPOINT ["node","server.js"]`, so the process receives SIGTERM. Add a minimal init (`tini`, or `--init`) if the process spawns children.
- **Define a `HEALTHCHECK`** in the Dockerfile, or a liveness or readiness probe in the orchestrator.
- **Add OCI labels:** `org.opencontainers.image.source`, `.revision` and `.version`.
- **Configure through environment variables only.** The image is the same in every environment (twelve-factor).

## Compose

- **Base and override files.** `compose.yaml` defines the full local stack: the app plus its dependencies, at the same versions as production. `compose.override.yaml` adds dev conveniences such as bind mounts and hot reload.
- **Wait for healthy dependencies.** Use `depends_on` with `condition: service_healthy`, and give every dependency a healthcheck.
- **Persist data in named volumes.** Use explicit networks, and expose only the ports a developer needs.
- **Commit a `.env.example`** with every variable and safe placeholder values. Keep `.env` in `.gitignore`.
- **Running `docker compose up` from a clean clone must produce a working app.** That is the acceptance test.

## Image tagging

- **Tag each image with the immutable git SHA** (`app:3f2a9c1`), plus the semver version on release (`app:1.4.0`). A moving tag like `latest` is for humans only. Deployments reference the SHA tag, or the digest.
- **Build an image once and promote that same image** through each environment. Never rebuild an image for each environment.
