# 🛡️ Scenario: Blackthorn Defense Ltd. — Second Pass Security Audit

---

# 🚨 IMPORTANT — CHALLENGE INFRASTRUCTURE RULES

The infrastructure for this challenge is hosted by **Woodside Networks**.  

❌ Any actions that would be harmful to the infrastructure or impact other competitors are **STRICTLY PROHIBITED**.  

This includes, but is not limited to:  
- ⛏️ **Cryptocurrency mining** (e.g. Bitcoin mining)  
- 🔍 **Port scanning** or attacking infrastructure components **outside your assigned environment**  
- 🛑 **Exploiting or tampering** with systems such as the WAN gateway, shared network resources, or the hosting platform  
- 🎯 **Attacking, scanning, or interfering** with other competitors’ systems or challenge environments  

**⚠️ Any such activity will result in _IMMEDIATE DISQUALIFICATION_ and may lead to _REMOVAL FROM THE COMPETITION_.**

## 🏢 Company profile
Blackthorn Defense Ltd. is an Irish defense technology manufacturer with headquarters in Dublin and a production facility in Cork. 

The company designs and supplies advanced battlefield communication systems, drone platforms, and electronic countermeasure units for allied nations.

Recently, Blackthorn provided **technology critical to the production of drones** used by Ukraine’s armed forces, significantly increasing their visibility to — and potential targeting by — **Russian state-sponsored cyber actors**.

---

## 📜 The backstory
Earlier this year, a contractor carried out a preventive security check. Several weaknesses were found, but the company decided against fixing them at the time due to the associated cost.

Now, with the **NIS2 Directive** transcribed into Irish law — bringing with it **potential criminal liability for company directors** in cases of serious cybersecurity failings — and given Blackthorn’s involvement in Ukraine’s defense supply chain increasing the risk of an attack, the board has changed course.  

The **previous contractor’s findings have been fully approved for remediation**, and you’ve been brought in to both **implement those changes** and **carry out a deeper assessment** to find any other security gaps worth addressing in the next phase.

---

## ✅ Approved findings to be rectified

### 1. 🔐 Directory Services & Authentication
- *[To be filled]*

### 2. 👥 Access Control
- *[To be filled]*

### 3. 🌐 Network Security
- *[To be filled]*

### 4. 🛠️ General Hardening
- *[To be filled]*

---

## 🖥️ Technical details
Blackthorn Defense maintains a **dedicated rack** in a commercial datacentre, which houses their critical servers, storage, and main firewall.  

They also have two key sites — the headquarters in Dublin and the production facility in Cork 
These sites are securely connected to the datacentre via **WireGuard tunnels**, forming a hub-and-spoke network with the DC at its core.

- **Private address ranges**:
  - Datacentre: `10.10.0.0/16`
  - Dublin headquarters: `172.16.0.0/16`
  - Cork factory: `172.17.0.0/16`
- **Connectivity**: Headquarters and factory connected to the datacentre via WireGuard tunnels.
- **Public addressing**: All public IPs in the `100.100.X.0/24` range (X = competitor number).
- **Access**:
  - 🖥️ Jump host inside the DC
  - 📡 Permission to scan all private ranges
  - 📄 Administrator access to **firewall configurations**
  - 📂 Administrator access to **Active Directory**

---

## 🔑 Login credentials
> **Note:** These accounts are provided for ease of use during the challenge. They are **not** intended to be part of the assessment scope or considered a security finding.

- **Firewalls**  
  - Username: `root`  
  - Password: `opnsense`  

- **Active Directory**  
  - Username: `administrator`  
  - Password: `Passw0rd!$`

---

## 🎯 Your mission
1. **Rectify the findings** from the previous assessment.
2. Perform a **full security assessment** of the environment.
3. Identify **any new issues** that should be considered for remediation in the next phase.

---

## 📑 Deliverables
- **Implementation report** detailing how the approved fixes were applied and verified.
- **New findings report** with supporting evidence and remediation recommendations.
- **Executive summary** linking remaining risks to NIS2 obligations, defense-sector targeting risks, and potential board liability.
