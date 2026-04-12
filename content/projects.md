+++
date = '2026-04-12'
draft = false
title = 'Projects'
+++

Hands-on labs demonstrating production-grade infrastructure patterns. Each one is designed to be read — the documentation, decisions, and CI are as much the point as the code.

## SRE & Operations

- **[SRE Observability Lab](https://github.com/R055LE/sre-observability-lab)** — SLO-based alerting with error budget burn-rate math, chaos engineering with documented expected outcomes, runbooks linked from alerts, request ID correlation across services, promtool-tested alert rules in CI.

- **[Go Deploy Lab](https://github.com/R055LE/go-deploy-lab)** — A Go application through the full deployment lifecycle: multi-stage distroless builds, Kubernetes manifests with security hardening, rolling updates, Kyverno policies, Prometheus metrics, Grafana dashboards.

## Security & Compliance

- **[Container Hardening Lab](https://github.com/R055LE/container-hardening-lab)** — CIS/Iron Bank-aligned container hardening: non-root builds, OPA/Kyverno policy enforcement, Cosign signing, SBOM generation, Falco runtime detection.

- **[IaC Security Lab](https://github.com/R055LE/iac-security-lab)** — Policy-as-code for Terraform with tfsec, Trivy, and OPA/Rego static analysis against CIS AWS Foundations Benchmark. No cloud credentials required.

## Platform

- **[K8s Bootstrap Lab](https://github.com/R055LE/k8s-bootstrap-lab)** — Production-grade Kubernetes platform bootstrap: GitOps, observability, and runtime security from Kind to EKS.

- **[MLOps Pipeline Lab](https://github.com/R055LE/mlops-pipeline-lab)** — Production-grade MLOps deployment pipeline: container hardening, CI/CD, GitOps, observability, and Kyverno policy enforcement around a HuggingFace model.
