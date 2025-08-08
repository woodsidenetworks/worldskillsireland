# Worldskills National Competition

# 🏁 NimbleGrid Solutions — VPN, Routing and Segmentation Challenge

# 🚨 IMPORTANT — CHALLENGE INFRASTRUCTURE RULES

The infrastructure for this challenge is hosted by **Woodside Networks**.  

❌ Any actions that would be harmful to the infrastructure or impact other competitors are **STRICTLY PROHIBITED**.  

This includes, but is not limited to:  
- ⛏️ **Cryptocurrency mining** (e.g. Bitcoin mining)  
- 🔍 **Port scanning** or attacking infrastructure components **outside your assigned environment**  
- 🛑 **Exploiting or tampering** with systems such as the WAN gateway, shared network resources, or the hosting platform  
- 🎯 **Attacking, scanning, or interfering** with other competitors’ systems or challenge environments  

**⚠️ Any such activity will result in _IMMEDIATE DISQUALIFICATION_ and may lead to _REMOVAL FROM THE COMPETITION_.**

---

## 📘 Overview

Welcome to the **NimbleGrid Solutions** infrastructure challenge.

Your task is to design and deploy secure inter-office networking using **OPNsense**, implementing both **site-to-site** and **remote access VPNs**, along with proper **routing**, **firewalling**, **network segmentation discipline**, and — most importantly — **security**.

This is a **realistic enterprise simulation**. Sloppy address planning, excessive supernetting, or overly permissive firewall rules will be penalised. Treat this as if you were deploying production infrastructure.

This challange is designed to replicate those which you would see in Worldskills International

---

## 🏢 Network Layout

NimbleGrid operates the following infrastructure across Ireland:

| Site Alias         | Location        | Role                                              |
|--------------------|------------------|---------------------------------------------------|
| **DC**             | Dublin Datacentre | Core services: AD, DNS, DHCP, SMB                |
| **Site A**         | Dublin Office     | Main office (Staff work here)                    |
| **Site B**         | Cork Office       | Satellite office (User B)                        |
| **Remote WFH User**| Galway (remote)   | Remote engineer accessing via OpenVPN            |

---

## 🌐 IP Allocations

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



## 🔐 VPN 

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


### ✅ Active Directory Setup

- Deploy a Windows Server instance in the **DC**.
- Install the appropriate **Active Directory Domain Services** role and promote it to a **Domain Controller**.
- Configure appropriate domain naming (e.g. `nimblegrid.local`).
- Enable and test **LDAP** and **LDAPS** services.
- Ensure DNS is installed and serving internal records for domain and infrastructure.
- Set DNS forwarders or root hints to forward external DNS queries appropriately.
- Create a baseline **OU structure** (e.g. Users, Computers, Admins).
- Define **user roles** and implement **RBAC** as appropriate.
- Use **Group Policy** to harden the domain and apply security policies.
- Make sure the AD server is reachable from authorised subnets only.


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

- Avoid supernetting (using large subnets such as /16)
- Avoid overlaps and keep headroom for future growth


> ⚡️ Extra points will be awarded for implementing hardening measures such as:
> - Enforcing least privilege access via firewall rules and **RBAC** with proper OU structure
> - Using **LDAPS (LDAP over TLS/SSL)** instead of unencrypted LDAP
> - Using **firewall aliases** and proper **naming/labelling** of rules
> - Creating **port group aliases** to efficiently bundle related services into consolidated rules
> - Explicitly allowing only needed services
> - Using certificate based authentication + Active Directory backed username/password authenticaiton
> - Individual user certificates
> - Strict CN (common name) checking on certificates with username as CN
> - Use of multi-factor authentication (MFA) or strong certificate-based auth

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

## 📊 Marking Scheme

| Category               | Weight |
|------------------------|--------|
| Security               | 20%    |
| VPN Configuration      | 20%    |
| Routing                | 15%    |
| Firewall Rules         | 15%    |
| Proper Subnetting      | 10%    |
| Testing                | 20%    |

---

## 🧥 Additional Notes & Tips

- No need to simulate public IPs — `100.64.x.0/24` is your internet
- If you have issues getting the pings to reply correctly then go back to basics
  - Check the tunnel is up,
  - Check you can ping the tunnel interfaces
  - Check if you can ping the subnet interface gateways
  - In other words, check each step of the route one by one.
  - Check firewall and tracert to troubleshoot
 
- In Active Directory usernames are known by two variables.
  - User Principal Name (username@domain.tld)
  - sAMAccountName (username or domain\username)
  - In OPNsense you will use sAMAccountName when configuring the VPN user auth.
 
- Secure by design and from the ground up is the objective to remember.

- Theres a strong possibility you won't complete every task / step at the end, and a strong chance some of it may not be perfect. This is a tough challange that is designed to test your problem solving and time management skills. Just relax and keep going. If you don't get something fully completed or working, document what you did. You can still get marks for a good setup even if you did not get a ping reply.

---

## 🔟 Final Advice

Segment properly.  
Route deliberately.  
Secure everything. 🛡️
