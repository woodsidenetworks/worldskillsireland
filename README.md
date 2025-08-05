# worldskillsireland

# 🏁 NimbleGrid Solutions — VPN, Routing and Segmentation Challenge

_This challenge infrastructure is provided by **Woodside Networks** for competition use._

🚨 **Important**: Competitors must **not** carry out any actions that would be harmful to the infrastructure. This includes, but is not limited to:
- Cryptocurrency mining (e.g. Bitcoin mining)
- Port scanning or attacking infrastructure components outside your assigned environment
- Attempting to exploit or tamper with systems such as the WAN gateway or other shared network resources

Any such actions will result in immediate disqualification and may lead to removal from the competition.

## 📘 Overview

Welcome to the **NimbleGrid Solutions** infrastructure challenge.

Your task is to design and deploy secure inter-office networking using **OPNsense**, implementing both **site-to-site** and **remote access VPNs**, along with proper **routing**, **firewalling**, and — most importantly — **network segmentation discipline**.

This is a **realistic enterprise simulation**. Sloppy address planning, excessive supernetting, or overly permissive firewall rules will be penalised. Treat this as if you were deploying production infrastructure.

---

## 🏢 Network Layout

NimbleGrid operates the following infrastructure across Ireland:

| Site Alias         | Location        | Role                                              |
|--------------------|------------------|---------------------------------------------------|
| **DC**             | Dublin Datacentre | Core services: AD, DNS, DHCP, file shares         |
| **Site A**         | Dublin Office     | Main office (staff work here)                     |
| **Site B**         | Cork Office       | Satellite office (User B)                         |
| **Remote WFH User**| Galway (remote)   | Remote engineer accessing via OpenVPN             |

---

## 🌐 Subnet Plan

> Each competitor is assigned a **WAN block**: `100.64.x.0/24`  
> This represents your slice of the “imaginary internet” using CGNAT space.

| Site Alias         | LAN Subnet         | WAN IP             | WAN Gateway     |
|--------------------|--------------------|---------------------|------------------|
| DC                 | `10.10.0.0/16`      | `100.64.x.10`       | `100.64.x.1`     |
| Site A             | `172.16.10.0/24`    | `100.64.x.11`       | `100.64.x.1`     |
| Site B             | `172.16.20.0/24`    | `100.64.x.12`       | `100.64.x.1`     |
| Remote WFH User    | `100.64.x.50`       | `100.64.x.50`       | `100.64.x.1`     |

> ⚠️ You are assigned a large subnet allocation for the **DC**, but:
>
> - **Do not assign the entire allocation or a large supernet to any interface**
> - Subnet carefully into smaller logical ranges, for example:
>   - A subnet for core infrastructure (e.g. AD, DNS)
>   - A subnet for VPN tunnel endpoints
>   - A subnet for remote access or DMZ
> - Leave room for VLANs, user/device isolation, or future services

📅 Thoughtful subnetting and clean segmentation will be assessed during marking.

---

## 🔐 VPN Requirements

> ⚡️ Extra points will be awarded for implementing hardening measures such as:
> - Enforcing least privilege access via firewall rules and **RBAC**
> - Using **LDAPS (LDAP over TLS/SSL)** instead of unencrypted LDAP
> - Using **firewall aliases** and proper **naming/labelling** of rules
> - Creating **port group aliases** to efficiently bundle related services into consolidated rules
> - Explicitly allowing only needed services
> - Use of multi-factor authentication (MFA) or strong certificate-based auth

### VPN Topology Design

The **OpenVPN tunnel must operate as a full-tunnel VPN**. All traffic from the **Remote WFH User**, including internet-bound traffic, must be routed through the **DC firewall** to ensure traffic is protected from snooping on untrusted networks (e.g. public Wi-Fi).

- **WireGuard tunnels** must be established:
  - From **Site A → DC**
  - From **Site B → DC**
- **OpenVPN** server must run at the **DC**:
  - The **Remote WFH User** connects directly to the **DC firewall**
  - The VPN must function as a **full-tunnel**, forcing all traffic through the DC
  - Only minimal service-specific rules should be applied to internal resources
  - General internet access should also be securely routed through the DC gateway
  - The VPN-assigned IP is `100.64.x.50` from the allocated WAN range

> ♻️ All inter-site and remote traffic must pass through the DC. The DC must handle routing, DNS, and firewalling between all internal networks.

---

## ⚙️ Technical Requirements

### ✅ VPN Setup

- Configure **WireGuard** between **Site A**, **Site B**, and **DC**
- Configure **OpenVPN** at the **DC**
  - Assign static IP to **Remote WFH User**
  - Enforce least privilege by limiting to specific destinations and ports

### ✅ Routing

- Ensure:
  - Site A ↔ DC communication
  - Site B ↔ DC communication
  - Remote WFH User ↔ permitted services only
  - Return traffic flows correctly

### ✅ Firewall Rules

- Define clear firewall policies per zone and interface:
  - Allow VPN traffic on WAN
  - Define **per-interface LAN rules**
  - Create **aliases** for grouped services and ports (e.g. port group aliases for domain services)
  - Clearly label all rules and describe their purpose
  - Restrict Remote WFH User to authorised services and ports only

### ✅ Subnetting Best Practices

- No single interface should use a `/16`
- Use `/24` or smaller per segment
- Avoid overlaps and keep headroom for future growth

---

## 🧩 Test Scenarios

| Source             | Destination          | Expected Result | Path                          |
|--------------------|----------------------|------------------|-------------------------------|
| Remote WFH User    | DC (LDAPS, DNS)      | ✅ Success       | OpenVPN                       |
| Remote WFH User    | Site B (general LAN) | ❌ Blocked       | OpenVPN → DC → WireGuard     |
| Site B             | DC                   | ✅ Success       | WireGuard site-to-site        |
| Site A             | DC                   | ✅ Success       | WireGuard site-to-site        |

---

## 📆 Submission Checklist

Submit the following as part of your solution:

- `/docs/network-diagram.png` — A labelled diagram of all interfaces, tunnels, subnets
- `/configs/*.txt` — Configuration exports or CLI outputs (e.g. from OPNsense)
- `/tests/ping-results.txt` — Output of ping tests, DNS lookups, and AD join tests
- `/README.md` — Summary of your network design, firewall rules, and routing decisions
- *(Optional)* OPNsense `.xml` backup files for each node

---

## 🧠 Bonus Tasks (Optional)

- Implement **split tunnelling** for the Remote WFH User
- Set up **internal DNS** resolution using the AD DNS server
- Deploy **Suricata** or **ntopng** on OPNsense for traffic inspection
- Use **aliases and rule groups** for maintainable firewall rules
- Use **port group aliases** for services like AD, DNS, LDAPS, etc.
- Simulate VLANs or tagged interfaces in Site A
- Use **LDAPS**, secure administrative ports, and strict certificate-based VPN auth

---

## 📊 Marking Scheme

| Category               | Weight |
|------------------------|--------|
| Proper subnetting      | 25%    |
| VPN configuration      | 25%    |
| Routing completeness   | 20%    |
| Firewall correctness   | 15%    |
| Documentation & tests  | 15%    |

---

## 🧥 Additional Notes

- You may simulate WAN links using NAT or internal-only virtual networks
- Accepted platforms:
  - **VirtualBox**, **Proxmox**, **VMware**, **GNS3**, **EVE-NG**
- No need to simulate public IPs — `100.64.x.0/24` is your internet
- Dynamic DNS is optional but encouraged for realism

---

## 🔟 Final Advice

Segment properly.  
Route deliberately.  
Secure everything. 🛡️
