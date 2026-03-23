# Policy as Code

AI/ML infrastructure governance using Sentinel and OPA Gatekeeper to enforce cost controls, security policies, and EU AI Act compliance at both the Terraform plan stage and Kubernetes admission time.

## Overview

Organisations deploying AI workloads face a real problem: a single unapproved GPU instance can rack up thousands in monthly compute costs, and untracked model deployments create compliance nightmares under frameworks like the EU AI Act. This project implements a two-layer policy enforcement architecture that catches violations before they ever reach production.

The first layer uses HashiCorp Sentinel to gate Terraform plans — blocking unapproved GPU instances, enforcing mandatory resource tagging for cost attribution, and capping monthly infrastructure spend. The second layer uses OPA Gatekeeper as a Kubernetes admission controller, ensuring every ML model deployment carries proper version tracking, team attribution, and model registry references before it's admitted to the cluster.

The underlying infrastructure provisions an EKS cluster with KMS-encrypted secrets, a versioned S3 bucket for model artifacts, and a VPC with flow logging — all tagged for ML cost allocation across multiple teams.

## Architecture

![](screenshots/cloud-architecture.png)

Sentinel policies run at plan time inside Terraform Cloud, validating infrastructure changes against four policies (GPU controls, resource tagging, spending limits, and model deployment rules) before any AWS resources are provisioned. Once infrastructure is live, OPA Gatekeeper operates as an admission webhook on the EKS cluster, evaluating every Deployment and StatefulSet against constraint templates that enforce label requirements and model registry validation. This creates a defence-in-depth approach where policy violations are caught at two distinct stages of the deployment lifecycle.

## Tech Stack

**Infrastructure**: AWS EKS, VPC, S3, KMS, Terraform

**Policy Enforcement**: HashiCorp Sentinel (4 policies), OPA Gatekeeper (2 constraint templates + 2 constraints)

**Compliance**: EU AI Act risk classification, mandatory ML governance labels, model registry validation

**Cost Controls**: GPU instance restriction, per-team budget caps, automated spend limits

## Key Decisions

- **Two-layer enforcement over single-layer**: Sentinel catches infrastructure-level violations (wrong instance types, missing tags) at plan time, while OPA catches workload-level violations (unregistered models, missing version labels) at deploy time. Neither layer alone covers both concerns.

- **Hard-mandatory vs. advisory enforcement levels**: GPU controls and tagging are hard-mandatory because violations have immediate financial or compliance impact. Model deployment rules are advisory to avoid blocking experimentation in non-production environments.

- **KMS encryption for both EKS secrets and S3 model artifacts**: A single KMS key encrypts cluster secrets and model storage, simplifying key management while ensuring model IP is protected at rest across both storage layers.

- **Cost-effective demo architecture**: Uses t3.medium instances with GPU node groups commented but fully defined, demonstrating production-ready GPU scaling patterns without incurring GPU costs.

## Screenshots

![](screenshots/1.png)

![](screenshots/2.png)

![](screenshots/3.png)

![](screenshots/4.png)

![](screenshots/5.png)

![](screenshots/6.png)

![](screenshots/7.png)

![](screenshots/8.png)

![](screenshots/9.png)

![](screenshots/10.png)

## Author

**Noah Frost**

- Website: [noahfrost.co.uk](https://noahfrost.co.uk)
- GitHub: [github.com/nfroze](https://github.com/nfroze)
- LinkedIn: [linkedin.com/in/nfroze](https://linkedin.com/in/nfroze)
