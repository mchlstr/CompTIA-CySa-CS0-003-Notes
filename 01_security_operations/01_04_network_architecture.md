# Network Architecture

## On-premises, Cloud, Hybrid (network angle)

- **On-prem network** — physical switches/routers/firewalls, VLANs, MPLS or direct fiber. You control every hop.
- **Cloud network** — virtual constructs: VPCs (AWS), VNets (Azure), subnets, security groups, NACLs, route tables, transit gateways. Same logical concepts, different terminology per provider.
- **Hybrid network** — connects on-prem to cloud via VPN (IPsec) or dedicated circuits (AWS Direct Connect, Azure ExpressRoute, GCP Cloud Interconnect).

**Common pitfalls:**
- **East-west traffic blind spots** — perimeter firewalls don't see traffic between cloud workloads or between VLANs.
- **Misconfigured security groups** — `0.0.0.0/0` on port 22 is the cloud equivalent of leaving the front door open.
- **Public S3/blob storage** — accidental data exposure; major breach source.

## Network segmentation

**Segmentation** = dividing the network into zones so a breach in one doesn't reach others. Limits blast radius.

Approaches:
- **Physical segmentation** — separate switches/cables. Strongest, most expensive.
- **VLANs** — logical separation at Layer 2. Common, but VLAN hopping is possible if misconfigured.
- **Subnets + ACLs** — Layer 3 separation with router/firewall rules.
- **Microsegmentation** — workload-level (e.g., one server can only talk to specific others). Tools: VMware NSX, Illumio, cloud security groups.
- **Air gap** — physically isolated network. Used for OT/ICS, classified systems. Increasingly rare in practice.

**Common zones:** Internet → DMZ → internal → trusted (servers) → restricted (PCI, OT). Each zone separated by firewall.

## Zero Trust

**Zero Trust (ZT)** = "never trust, always verify." Assumes the network is already compromised, so every request must be authenticated and authorized regardless of location.

Core principles:
- **Verify explicitly** — every request authenticated (user + device + context).
- **Least privilege** — minimal access, just-in-time where possible.
- **Assume breach** — design as if attackers are already inside.

Building blocks:
- **Identity-centric** — strong auth (MFA), conditional access policies.
- **Device posture** — only compliant/managed devices get access.
- **Microsegmentation** — workload-to-workload policies, not network-zone trust.
- **Continuous monitoring** — behavior, not just point-in-time auth.

**ZTNA (Zero Trust Network Access)** replaces VPNs: instead of putting users on the network, it brokers per-app access.

**Reference:** NIST SP 800-207.

## SASE (Secure Access Service Edge)

**SASE** (pronounced "sassy") = networking + security delivered as a cloud service.

Combines:
- **SD-WAN** (the WAN connectivity layer)
- **SWG** (Secure Web Gateway)
- **CASB** (Cloud Access Security Broker)
- **ZTNA** (Zero Trust Network Access)
- **FWaaS** (Firewall-as-a-Service)
- **DLP** (Data Loss Prevention)

The pitch: instead of backhauling all branch traffic to HQ for inspection, route it through a globally-distributed cloud security stack (Zscaler, Netskope, Cloudflare, Palo Alto Prisma).

**SSE (Security Service Edge)** = SASE minus the SD-WAN piece (just the security part).

## SDN (Software-Defined Networking)

**SDN** = separating the network **control plane** (decisions about where traffic goes) from the **data plane** (actually forwarding packets).

- A central **controller** has a global view, programs the switches via APIs (e.g., OpenFlow).
- Enables network programmability — dynamic, automated policy changes.
- Foundation for SD-WAN, NFV (Network Function Virtualization), cloud networking.

**Security implications:**
- **Controller is critical** — compromise = own the network.
- **API security** matters more than ever.
- **Easier microsegmentation, faster incident response** (block a host network-wide instantly).

**Exam tip:** SDN ≠ SD-WAN. SDN is the underlying tech; SD-WAN is one application of SDN principles to wide-area networks.
