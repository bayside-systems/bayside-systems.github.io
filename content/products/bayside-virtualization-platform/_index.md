---
title: "Bayside Virtualization Platform"
description: "Isolated workloads as Xen domains, with hardware, storage, networking, policy, and the operator interface in separate domains."
weight: 2
---

## Isolated Workloads as Xen Domains

The Bayside Virtualization Platform runs isolated workloads on one computer. Each workload is a Xen domain. Hardware, storage, networking, policy, and the operator interface each run in their own domains. A workload receives a device, a file, or a byte path only when policy allows that path.

If your workload touches sensitive data, runs untrusted code, or must stay contained away from the rest of the machine, the platform is that boundary.

### Why Domain-Level Isolation?

Containers and traditional VMs share process namespaces, device drivers, and network paths with the host. Treating a compromised workload like an ordinary application is a category error.

The risks are concrete:

- **Compromised-workload blowout** — a compromised domain stays inside its own domain. It cannot become the toolstack, claim another tenant's pages, or cross a label boundary
- **Device leakage** — each physical NIC and each PCI device belongs to exactly one driver domain
- **Opaque data flow** — inter-domain traffic is mediated by the data plane, so it can be audited end to end

The platform contains these risks at the hypervisor layer, so a compromised workload cannot compromise the system around it.

### What the Platform Provides

**Domain Role Model**  
Pinned domain identifiers for every platform role: constructor (domain 0), Xenstore (1), DomC (2), storage (3), network domains (4 and up), hardware (10), recovery (11), and console (12). An installation must not renumber the reserved roles.

**Hypervisor-Level Policy**  
XSM/FLASK policy is enforced in the hypervisor, and MLS is always on. Type enforcement and MLS are separate checks — a flow needs both. Tenant layers (`s0`, `s1`, and up) and isolation groups (`c1`, `c2`, ...) use label equality: a higher layer cannot read a lower layer, and a high-to-low write goes through the filter path.

**Measured Boot**  
The target boot chain is UEFI Secure Boot, then the DRTM coordinator, then the Secure Loader, then `xen.efi`. AMD SKINIT measures the Secure Loader into the DRTM PCR, and the Secure Loader measures the Xen image and the launch resource table. Intel TXT support is under construction in the same repository.

**Workload Shapes**  
The platform ships four workload shapes:

| Shape | Isolation unit |
| --- | --- |
| Computelet | One function. A diskless PVH domain running a prepared guest image |
| Container VM | One OCI container per PVH domain, on a fixed operator-selected kernel |
| Kubernetes pod | One or more container VMs plus one pod network VM |
| HVM guest | One traditional VM with a qcow2 disk — specified, but disabled on the live image |

Each Kubernetes container is its own domain. Init containers are domains that run in order, and sidecars are domains on the same pod network VM.

**Cross-Label Byte Path**  
Two workload domains with different MLS or MCS labels have no direct Argo path. Bytes travel through a wasmloader filter domain: the filter factory compiles the WebAssembly module, and the wasmloader runs it against a socket allowlist.

**Operator Interfaces**  
The Control Center (Qt operator console) manages computelet and VM lifecycle. The Administration Studio (Qt authoring console) authors zones, networks, and filters. Both run as unprivileged computelets and write artifacts that the policy plane accepts.

**Display and Input**  
One compositor process owns the physical display — it is the DRM master and the input seat owner. Guests publish frames with the Xen display protocol and the compositor composes them onto one KMS output. Windows guests use the same protocol through the WDDM driver.

### Two Deployments, One Design

The platform serves two deployments from one design. A single computer can present a desktop through the compositor, and a cluster can run headless workloads with explicit data flows. Both deployments use the same domain roles, the same policy, and the same device protocols.

### Built for High-Assurance Environments

For defense, intelligence, healthcare, and regulated industries, the platform is designed to meet the bar where a data leak is not a bug but an incident:

- No classified or regulated data reaches an untrusted endpoint unless policy permits it
- Strict, auditable control over what information each workload can access
- Evidence-grade logs for compliance, incident response, and accreditation
- Deployment patterns aligned with NIST, FedRAMP, and equivalent frameworks

### Use Cases

- Running untrusted or sensitive workloads on one machine, fully contained
- Multi-tenant agent and container platforms where tenants must remain isolated by label
- Classified or CUI workloads that need virtualization without AI-shaped data leaks
- Defense and government deployments that require measured boot and domain-level policy

---

**Ready to run your workloads in a real boundary?**  
[Contact us](/contact) to discuss Bayside Virtualization Platform deployment options.
