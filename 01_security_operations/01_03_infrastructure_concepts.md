# Infrastructure Concepts

## Serverless

**Serverless** = you write functions (code), the cloud provider runs them on demand. No servers to patch, scale, or manage. Examples: **AWS Lambda, Azure Functions, Google Cloud Functions**.

- **Pricing:** per invocation + execution time (often pennies for low traffic).
- **Scaling:** automatic, near-instant.
- **Security model:** provider handles infrastructure; *you* are responsible for code, IAM permissions, secrets, dependencies.
- **Risks:** over-permissioned IAM roles, vulnerable third-party libraries, event-injection attacks, cold-start latency leaking timing info.

## Virtualization

**Virtualization** = running multiple virtual machines (VMs) on one physical host using a **hypervisor**.

- **Type 1 (bare metal)** — runs directly on hardware. Examples: VMware ESXi, Hyper-V, KVM, Xen. Used in data centers.
- **Type 2 (hosted)** — runs on top of an OS. Examples: VirtualBox, VMware Workstation. Used on desktops.

**Security concerns:**
- **VM escape** — guest breaks out to the hypervisor (rare but catastrophic, e.g., VENOM, Cloudburst).
- **Snapshot management** — old snapshots may contain unpatched OS or sensitive data.
- **VM sprawl** — unused VMs accumulate, unpatched, forgotten.
- **Resource contention / side-channel attacks** — Spectre/Meltdown, L1TF.

## Containerization

**Containers** = lightweight, isolated processes sharing the host kernel. Faster than VMs, smaller, but weaker isolation.

- **Engine:** Docker (the original), containerd, CRI-O.
- **Orchestration:** Kubernetes (the standard), Docker Swarm, Nomad.
- **Image registries:** Docker Hub, ECR, GCR, Harbor.

**Security concerns:**
- **Vulnerable base images** — pull `latest`, get whatever's in there. Always pin and scan.
- **Container escape** — break out of container to host (much easier than VM escape).
- **Misconfigured Kubernetes** — exposed dashboards, overly permissive RBAC, secrets in environment variables.
- **Supply chain** — compromised images on public registries.
- **Secrets management** — never bake into images; use vaults (HashiCorp Vault, AWS Secrets Manager, K8s Secrets).

**Tools:** Trivy, Clair, Anchore (image scanning); Falco (runtime detection); kube-bench (CIS benchmark for K8s).

## Deployment models: On-premises, Cloud, Hybrid

| Model | What it is | Pros | Cons |
|---|---|---|---|
| **On-premises** | You own the hardware in your own data center | Full control, data stays on-site, predictable cost | High CapEx, slow scaling, you patch everything |
| **Cloud (public)** | Rented from AWS/Azure/GCP | Elastic, OpEx, global reach, managed services | Less control, vendor lock-in, ongoing cost, shared responsibility |
| **Private cloud** | Cloud-style infra on dedicated hardware (yours or hosted) | Cloud benefits + isolation | Expensive, complex |
| **Hybrid** | Mix of on-prem and cloud, integrated | Flexibility, gradual migration, regulatory compliance | Complex, multiple security models to manage |
| **Multi-cloud** | Multiple cloud providers | Avoid lock-in, redundancy | Even more complex, more attack surface |

**Shared responsibility model** (critical for exam):
- **IaaS** (you rent VMs) — provider secures hardware/hypervisor; *you* secure OS, apps, data, IAM.
- **PaaS** (you rent a platform like App Service, Heroku) — provider secures up to runtime; *you* secure app code, data, IAM.
- **SaaS** (you use Office 365, Salesforce) — provider secures almost everything; *you* secure data, user access, configuration.

**Exam tip:** "who patches the OS in IaaS?" → the customer. "Who patches the OS in PaaS?" → the provider.
