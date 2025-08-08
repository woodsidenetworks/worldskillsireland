# Scenario: GaelSec Systems Ltd. — Second Pass Security Audit

## Company profile
GaelSec Systems Ltd. is a fast-growing Irish smart industrial automation company with headquarters in Dublin and a satellite office in Cork. They provide automation solutions to critical industries across the EU and have grown rapidly in the past three years.

---

## The backstory
Earlier this year, a contractor carried out a preventive security check. Several weaknesses were found, but the company decided against fixing them at the time.

Now, with **NIS2** in Irish law and company directors facing **criminal liability** for certain security failings, the board has changed course. The **previous contractor’s findings have been fully approved for remediation**, and you’ve been brought in to both **implement those changes** and **carry out a deeper assessment** to find any other security gaps worth addressing in the next phase.

---

## Approved findings to be rectified

### 1. Directory Services & Authentication
- *[To be filled]*

### 2. Access Control
- *[To be filled]*

### 3. Network Security
- *[To be filled]*

### 4. General Hardening
- *[To be filled]*

---

## Technical details
- **Private address ranges**:
  - Datacentre: `10.10.0.0/16`
  - Dublin office: `172.16.0.0/16`
  - Cork office: `172.17.0.0/16`
- **Connectivity**: Offices connected to the datacentre via WireGuard tunnels.
- **Public addressing**: All public IPs in the `100.100.X.0/24` range (X = competitor number).
- **Access**:
  - Jump host inside the DC
  - Permission to scan all private ranges
  - Read-only access to firewall configurations and Active Directory

---

## Your mission
1. **Rectify the approved findings** from the previous assessment.
2. Perform a **fresh full security assessment** of the environment.
3. Identify **any new issues** that should be considered for remediation in the next phase.

---

## Deliverables
- **Implementation report** detailing how the approved fixes were applied and verified.
- **New findings report** with supporting evidence and remediation recommendations.
- **Executive summary** linking remaining risks to NIS2 obligations and potential board liability.
