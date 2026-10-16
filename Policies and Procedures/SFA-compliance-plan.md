# UChicago DSI Cluster — NIST CSF 2.0 SFA Compliance Plan

**Target Tier:** Tier 2 (Risk Informed)
**Branch:** `policies-and-procedures`
**Beads Prefix:** `sfa`
**Date:** 2026-03-31

---

## Changelog

| Date | Action |
|------|--------|
| 2026-04-06 | **Plan executed.** All 12 consolidated policy/procedure documents created as Jekyll pages at `/sfa-policies/`. Compliance action items checklist created at `/sfa-policies/compliance-actions/`. CS department policies incorporated. All 71 controls addressed. Pages are live but not linked from main navigation (draft status). Duplicate CSVs removed. |
| 2026-04-01 | Downloaded all 15 CS security policy documents from Google Docs for review. Added per-policy evaluation checklist to plan (see "CS Policy Evaluation Queue" section). Policies will be added to the Jekyll site individually after approval. Open question #2 partially resolved — docs are now accessible for review. |
| 2026-03-31 | Initial plan created. |

---

## Executive Summary

The University of Chicago's Security Framework Assessment (SFA) requires DSI to document compliance with 71 NIST CSF 2.0 controls. Each control requires both a **policy** (high-level intent — the "what" and "why") and a **procedure** (operational steps — the "how," "who," and "when").

| Status | Count | Action Required |
|--------|------:|-----------------|
| Have policy, need procedure | 15 | Write matching procedures |
| Need both policy + procedure | 56 | Write both |
| Have procedure | 0 | — |
| **Total** | **71** | |

### Tier 2 Hard Gate

Control **ID.AM-03** explicitly requires a **network architecture diagram** showing internal and external data flows. This is stated as "a necessary (but not sufficient) requirement to choose Tier 2."

---

## Existing Documentation That Maps to SFA Controls

Much of what DSI already does is documented on the cluster site but not framed as formal SFA artifacts. These can be formalized with relatively low effort.

| Existing Document | Path | SFA Controls Addressed |
|-------------------|------|------------------------|
| General Policies | `_policies/general.md` | PR.AA-01, PR.AA-05, PR.DS-01, PR.DS-02, DE.CM (partial) |
| Purchasing Policies | `_policies/purchasing.md` | ID.AM-08 (lifecycle management) |
| Techstaff SLA | `_policies/techstaff-sla.md` | RS.CO-02, RS.CO-03, RS.MA-03 (partial) |
| Getting Access | `pages/quickstart/accounts.md` | PR.AA-01, PR.AA-02 |
| SSH Setup | `pages/quickstart/ssh.md` | PR.AA-03 |
| Cluster Information | `pages/faq/cluster-information.md` | ID.AM-01, ID.AM-03 (partial) |
| Shared Storage | `pages/using-the-cluster/shared-storage.md` | PR.DS-11 |
| Scheduled Maintenance | `pages/using-the-cluster/scheduled-maintenance.md` | PR.PS-02, PR.PS-03 |
| FAQ | `pages/faq/faq.md` | PR.PS-05, PR.AA (partial) |
| 15 CS Security Policies (in Google Docs) | Google Docs (see "Controls with Existing Policies" below); pending individual review before site import | Various (see "Controls with Existing Policies" and "CS Policy Evaluation Queue" below) |

---

## Controls with Existing Policies (Need Procedures Only)

These 15 controls have policy documents in Google Docs. Each needs a corresponding procedure document. **Before importing to the Jekyll site, each policy must be individually reviewed and approved** (see "CS Policy Evaluation Queue" below).

| Control ID | Description | Policy Link |
|------------|-------------|-------------|
| ID.AM-01.01 | Hardware inventory maintenance | [Google Doc](https://docs.google.com/document/d/1ty6FZOiMPqbWbryA-8D6DfxTXoOlxgrr/edit) |
| ID.RA-02.01 | Cyber threat intelligence received from sharing forums | [Google Doc](https://docs.google.com/document/d/1eAuv0N9B_tdDl8p7fWMfS0RtUGDRn5lU/edit) |
| ID.RA-03.01 | Internal/external threats identified and recorded | [Google Doc](https://docs.google.com/document/d/19Rk6xnk1gMqe7KdZweY8R2CGj_ybn63T/edit) |
| ID.RA-04.01 | Potential impacts/likelihoods identified and recorded | [Google Doc](https://docs.google.com/document/d/1NvlZuHlJDYDvudc1ehmY7RckyPE1l-CK/edit) |
| ID.RA-05.01 | Risk understanding and response prioritization | [Google Doc](https://docs.google.com/document/d/1MAZ0AYWDi_tXZkHzjcmHJecieKk1EwnW/edit) |
| ID.RA-06.01 | Risk responses chosen, tracked, and communicated | [Google Doc](https://docs.google.com/document/d/157efL60IB4YRENG7EA9WJnGuQ_H9ENdi/edit) |
| PR.AT-01.01 | General cybersecurity awareness and training | [Google Doc](https://docs.google.com/document/d/1o_pnOzyoYairTWRCh-qimS9DWLpFOhM0/edit) |
| PR.AT-02.01 | Specialized role cybersecurity training | [Google Doc](https://docs.google.com/document/d/1mTqPM0r1SNUOFi4ux6hEZj6iy5RtaIgX/edit) |
| DE.CM-01.01 | Network monitoring for adverse events | [Google Doc](https://docs.google.com/document/d/1Xl76_zOHUnjS5dPj7ADApkQI1L-8CCGA/edit) |
| DE.CM-02.01 | Physical environment monitoring | [Google Doc](https://docs.google.com/document/d/1S-EHKntoIBbepO_k8vZZr7941juKnOVR/edit) |
| DE.AE-02.01 | Adverse event analysis | [Google Doc](https://docs.google.com/document/d/1146noo8wOlR_C6GxiVqDAx0neIS_6EhO/edit) |
| DE.AE-03.01 | Information correlated from multiple sources | [Google Doc](https://docs.google.com/document/d/1FtNp6rY-Ie3J6vzD7VKhxIdzR1Xb0SoN/edit) |
| RS.MI-01.01 | Incident containment | [Google Doc](https://docs.google.com/document/d/1VhT65Wph6q0uQG19-6UwnYT2OBkeIfRe/edit) |
| RS.MI-02.01 | Incident eradication | [Google Doc](https://docs.google.com/document/d/1Lzt5PwH1_C67E55huAomsFGY-ta7L20g/edit) |
| RC.CO-03.01 | Recovery activities communicated to stakeholders | [Google Doc](https://docs.google.com/document/d/1lJqgfUxLSYj7dArHa53s8HVmkf-skDhF/edit) |

---

## CS Policy Evaluation Queue

The 15 CS security policy documents below were downloaded from Google Docs on 2026-04-01. Each must be individually reviewed, approved, and then imported into the Jekyll site as `_policies/cs-<slug>.md` with corresponding navigation entries. Once approved, a policy should be added to the site and the procedure document can be written.

| # | Control ID | Policy Title | Proposed Site Slug | Reviewed | Approved | On Site |
|---|------------|-------------|-------------------|:--------:|:--------:|:-------:|
| 1 | ID.AM-01.01 | Third-party System and Service Inventory Policy | `cs-third-party-inventory` | [ ] | [ ] | [ ] |
| 2 | ID.RA-02.01 | Vulnerability Identification | `cs-vulnerability-identification` | [ ] | [ ] | [ ] |
| 3 | ID.RA-03.01 | Threat Identification and Documentation | `cs-threat-identification` | [ ] | [ ] | [ ] |
| 4 | ID.RA-04.01 | Risk Assessment Policy | `cs-risk-assessment` | [ ] | [ ] | [ ] |
| 5 | ID.RA-05.01 | Risk Calculation Policy | `cs-risk-calculation` | [ ] | [ ] | [ ] |
| 6 | ID.RA-06.01 | Risk Response Policy | `cs-risk-response` | [ ] | [ ] | [ ] |
| 7 | PR.AT-01.01 | Security Training for Personnel Policy | `cs-security-training` | [ ] | [ ] | [ ] |
| 8 | PR.AT-02.01 | Security Training for Admin Responsibilities Policy | `cs-admin-security-training` | [ ] | [ ] | [ ] |
| 9 | DE.CM-01.01 | Network Monitoring Policy | `cs-network-monitoring` | [ ] | [ ] | [ ] |
| 10 | DE.CM-02.01 | Physical Security Monitoring Policy | `cs-physical-security-monitoring` | [ ] | [ ] | [ ] |
| 11 | DE.AE-02.01 | Security Event Analysis Policy | `cs-security-event-analysis` | [ ] | [ ] | [ ] |
| 12 | DE.AE-03.01 | Security Event Impact Assessment Policy | `cs-security-event-impact` | [ ] | [ ] | [ ] |
| 13 | RS.MI-01.01 | Quarantine Policies for Impacted Systems | `cs-incident-quarantine` | [ ] | [ ] | [ ] |
| 14 | RS.MI-02.01 | Cyber Security Incident Mitigation Policy | `cs-incident-mitigation` | [ ] | [ ] | [ ] |
| 15 | RC.CO-03.01 | Communication of Recovery Activities | `cs-recovery-communication` | [ ] | [ ] | [ ] |

**Workflow for each policy:**
1. **Review** — Read the Google Doc and confirm content is accurate and appropriate for the DSI cluster context
2. **Approve** — Sign off on the policy (note any required edits)
3. **Import to site** — Create `_policies/cs-<slug>.md` with Jekyll front matter and add to `_data/navigation.yml`
4. **Write procedure** — Create the matching procedure document for the SFA control

---

## Implementation Plan: Consolidated Documents

Rather than 71 individual documents, related controls are grouped into ~12 consolidated policy-and-procedure documents across 3 phases.

### Phase 1 — Quick Wins & Hard Gates (~25 controls)

#### 1. Network Architecture Diagram
**Controls:** ID.AM-03
**Priority:** CRITICAL — Tier 2 gate requirement
**Deliverable:** Diagram showing login nodes, compute nodes, storage, management network, external connectivity, firewall boundaries, and data flow directions.
**What we know:** 32 nodes, 3296 cores, 164 GPUs, 2.1 PB storage. Need to document network topology, VLANs, and external access paths.

#### 2. Asset Management Policy & Procedure
**Controls:** ID.AM-01.01, ID.AM-02.01, ID.AM-08.01 (3 controls)
**Existing assets:**
- `cluster-information.md` has hardware inventory (nodes, GPUs, storage, network)
- `purchasing.md` covers lifecycle (5-year policy, NVIDIA EOL alignment)
- ID.AM-01.01 has an existing policy in Google Docs
**Gaps:**
- No formal software inventory (OS versions, SLURM, CUDA, drivers per node)
- No documented lifecycle management procedure
**Deliverable:** Combined document covering hardware inventory maintenance, software/services inventory, and lifecycle management procedures.

#### 3. Access Management Policy & Procedure
**Controls:** PR.AA-01.01 through PR.AA-06.01 (6 controls)
**Existing assets:**
- `accounts.md` describes CNetID requirement and PI approval process
- `ssh.md` documents SSH key authentication
- `general.md` prohibits shared accounts, references least privilege
- `faq.md` confirms no root access, no Docker (security)
**Gaps:**
- No formal identity proofing procedure
- No access review/recertification process
- No key rotation policy
- No physical access management documentation
- No documented least-privilege enforcement procedure
**Deliverable:** End-to-end access lifecycle document: identity proofing, provisioning, authentication, authorization, periodic review, deprovisioning, and physical access.

#### 4. Incident Response Plan
**Controls:** RS.MA-01.01 through RS.MA-05.01, RS.AN-03.01, RS.AN-06.01, RS.AN-07.01, RS.AN-08.01, RS.CO-02.01, RS.CO-03.01, RS.MI-01.01, RS.MI-02.01 (13 controls)
**Existing assets:**
- `techstaff-sla.md` defines P1/P2/P3 priorities and response times
- RS.MI-01 and RS.MI-02 have existing policies in Google Docs
**Gaps:**
- No formal incident response plan for security incidents
- No triage/validation procedure
- No escalation matrix (DSI techstaff -> UChicago IT Security -> CISO)
- No forensic evidence preservation procedures
- No root cause analysis process
- No incident declaration criteria
- No stakeholder notification templates
**Deliverable:** Full incident response plan covering: declaration criteria, triage, categorization, escalation, containment, eradication, evidence preservation, root cause analysis, stakeholder communication, and recovery initiation.
**HPC-specific scenarios to address:** compromised credentials, cryptomining on compute nodes, GPU driver vulnerabilities, unauthorized data access, resource exhaustion attacks.

### Phase 2 — Core Security (~24 controls)

#### 5. Risk Assessment Policy & Procedure
**Controls:** ID.RA-01.01, ID.RA-02.01, ID.RA-03.01, ID.RA-04.01, ID.RA-05.01, ID.RA-06.01, ID.RA-07.01, ID.RA-08.01, ID.RA-10.01 (9 controls; 5 have existing policies)
**Gaps:**
- No vulnerability identification/scanning procedure (ID.RA-01)
- No change management procedure (ID.RA-07)
- No vulnerability disclosure process (ID.RA-08)
- No supplier assessment process (ID.RA-10)
- 5 controls have policies but no procedures
**Deliverable:** Risk assessment framework covering vulnerability management, threat intelligence consumption, risk analysis, risk response tracking, change management, vulnerability disclosure handling, and supplier evaluation.

#### 6. Data Protection Policy & Procedure
**Controls:** PR.DS-01.01, PR.DS-02.01, PR.DS-10.01, PR.DS-11.01 (4 controls)
**Existing assets:**
- `general.md` covers data management, restricted data, storage allocation
- `shared-storage.md` documents storage tiers and backup status (home/project backed up, scratch not)
- SSH provides encryption in transit
**Gaps:**
- No explicit encryption-at-rest statement
- No data classification documentation
- No backup testing procedures
- No data-in-use protection documentation
**Deliverable:** Data protection document covering classification, encryption (at rest, in transit, in use), backup procedures, backup testing, and retention/disposal.

#### 7. Monitoring & Detection Policy & Procedure
**Controls:** DE.CM-01.01, DE.CM-02.01, DE.CM-03.01, DE.CM-06.01, DE.CM-09.01, DE.AE-02.01, DE.AE-03.01, DE.AE-04.01, DE.AE-06.01, DE.AE-07.01, DE.AE-08.01 (11 controls; 4 have existing policies)
**Existing assets:**
- `general.md` states "all activities on DSI systems are subject to monitoring"
- SLURM accounting provides job-level audit trails
**Gaps:**
- No documentation of specific monitoring tools in place
- No log collection/review procedures
- No personnel activity monitoring procedure
- No external service provider monitoring
- No incident declaration criteria
- No adverse event notification procedures
**Deliverable:** Monitoring and detection document covering: network monitoring, physical environment monitoring, personnel activity monitoring, external service monitoring, compute environment monitoring, event analysis, information correlation, impact assessment, event notification, threat intelligence integration, and incident declaration criteria.

#### 8. Platform Security Policy & Procedure
**Controls:** PR.PS-01.01 through PR.PS-06.01 (6 controls)
**Existing assets:**
- `scheduled-maintenance.md` documents maintenance windows (twice yearly for CUDA, Ubuntu, SLURM)
- `faq.md` shows no Docker (security), container limitations
**Gaps:**
- No configuration management documentation (Ansible playbooks, etc.)
- No formal patch management process
- No logging policy/procedure
- No unauthorized software prevention documentation beyond Docker prohibition
- No secure development practices
**Deliverable:** Platform security document covering: configuration management, software maintenance/patching, hardware maintenance, log generation and management, unauthorized software controls, and secure development lifecycle.

### Phase 3 — Remaining Controls (~22 controls)

#### 9. Security Awareness & Training Policy & Procedure
**Controls:** PR.AT-01.01, PR.AT-02.01 (2 controls; both have existing policies)
**Gaps:**
- Policies exist but no training program or tracking procedures
**Deliverable:** Training program document covering: general cybersecurity awareness training, specialized role training for techstaff, training records, and periodic refresher schedule.

#### 10. Infrastructure Resilience Policy & Procedure
**Controls:** PR.IR-01.01 through PR.IR-04.01 (4 controls)
**Gaps:**
- No network protection documentation
- No environmental threat protection documentation
- No resilience mechanisms documentation
- No capacity management documentation
**Deliverable:** Infrastructure resilience document covering: network access controls, environmental protections (power, cooling, fire suppression), resilience mechanisms, and capacity planning/monitoring.

#### 11. Recovery Plan & Procedure
**Controls:** RC.RP-01.01 through RC.RP-06.01, RC.CO-03.01, RC.CO-04.01 (8 controls; 1 has existing policy)
**Gaps:**
- No recovery plan
- No backup integrity verification procedure
- No post-incident operational norms
- No recovery closure criteria
- No public communication templates
**Deliverable:** Recovery plan covering: recovery execution procedures, action prioritization, backup integrity verification, post-incident norms, asset restoration validation, recovery closure criteria, and internal/external recovery communication.
**HPC-specific items:** Node rebuild from bare metal, SLURM database backup/restore, user data restoration, RTO/RPO definitions.

#### 12. Continuous Improvement Policy & Procedure
**Controls:** ID.IM-01.01 through ID.IM-04.01 (4 controls)
**Gaps:**
- No improvement identification processes from evaluations, tests, operations, or incident response
**Deliverable:** Continuous improvement document covering: evaluation-driven improvements, security test improvements, operational improvements, and incident response plan maintenance.

---

## Complete Control Inventory by NIST Function

### IDENTIFY (ID) — 17 controls

| Control | Description | Has Policy | Has Procedure | Planned Document |
|---------|-------------|:----------:|:-------------:|-----------------|
| ID.AM-01.01 | Hardware inventory maintenance | Yes | No | #2 Asset Management |
| ID.AM-02.01 | Software/services inventory maintenance | No | No | #2 Asset Management |
| ID.AM-03 | Network communication and data flow diagrams | No | No | #1 Network Diagram |
| ID.AM-08.01 | Systems/hardware/software lifecycle management | No | No | #2 Asset Management |
| ID.RA-01.01 | Vulnerability identification and recording | No | No | #5 Risk Assessment |
| ID.RA-02.01 | Cyber threat intelligence from sharing forums | Yes | No | #5 Risk Assessment |
| ID.RA-03.01 | Internal/external threat identification | Yes | No | #5 Risk Assessment |
| ID.RA-04.01 | Impact/likelihood identification | Yes | No | #5 Risk Assessment |
| ID.RA-05.01 | Risk understanding and prioritization | Yes | No | #5 Risk Assessment |
| ID.RA-06.01 | Risk response tracking and communication | Yes | No | #5 Risk Assessment |
| ID.RA-07.01 | Change/exception management with risk assessment | No | No | #5 Risk Assessment |
| ID.RA-08.01 | Vulnerability disclosure processes | No | No | #5 Risk Assessment |
| ID.RA-10.01 | Critical supplier assessment prior to acquisition | No | No | #5 Risk Assessment |
| ID.IM-01.01 | Improvements identified from evaluations | No | No | #12 Continuous Improvement |
| ID.IM-02.01 | Improvements from security tests/exercises | No | No | #12 Continuous Improvement |
| ID.IM-03.01 | Improvements from operational processes | No | No | #12 Continuous Improvement |
| ID.IM-04.01 | Incident response plan maintenance | No | No | #12 Continuous Improvement |

### PROTECT (PR) — 22 controls

| Control | Description | Has Policy | Has Procedure | Planned Document |
|---------|-------------|:----------:|:-------------:|-----------------|
| PR.AA-01.01 | Identity/credential management | No | No | #3 Access Management |
| PR.AA-02.01 | Identity proofing and credential binding | No | No | #3 Access Management |
| PR.AA-03.01 | User/service/hardware authentication | No | No | #3 Access Management |
| PR.AA-04.01 | Identity assertion protection and verification | No | No | #3 Access Management |
| PR.AA-05.01 | Access permissions, least privilege, separation of duties | No | No | #3 Access Management |
| PR.AA-06.01 | Physical access management | No | No | #3 Access Management |
| PR.AT-01.01 | General cybersecurity awareness training | Yes | No | #9 Training |
| PR.AT-02.01 | Specialized role cybersecurity training | Yes | No | #9 Training |
| PR.DS-01.01 | Data-at-rest confidentiality/integrity/availability | No | No | #6 Data Protection |
| PR.DS-02.01 | Data-in-transit confidentiality/integrity/availability | No | No | #6 Data Protection |
| PR.DS-10.01 | Data-in-use confidentiality/integrity/availability | No | No | #6 Data Protection |
| PR.DS-11.01 | Backup creation, protection, maintenance, testing | No | No | #6 Data Protection |
| PR.PS-01.01 | Configuration management practices | No | No | #8 Platform Security |
| PR.PS-02.01 | Software maintenance commensurate with risk | No | No | #8 Platform Security |
| PR.PS-03.01 | Hardware maintenance commensurate with risk | No | No | #8 Platform Security |
| PR.PS-04.01 | Log record generation for continuous monitoring | No | No | #8 Platform Security |
| PR.PS-05.01 | Unauthorized software prevention | No | No | #8 Platform Security |
| PR.PS-06.01 | Secure software development practices | No | No | #8 Platform Security |
| PR.IR-01.01 | Network/environment protection from unauthorized access | No | No | #10 Infrastructure Resilience |
| PR.IR-02.01 | Technology asset environmental protection | No | No | #10 Infrastructure Resilience |
| PR.IR-03.01 | Resilience mechanisms for normal/adverse situations | No | No | #10 Infrastructure Resilience |
| PR.IR-04.01 | Adequate resource capacity for availability | No | No | #10 Infrastructure Resilience |

### DETECT (DE) — 11 controls

| Control | Description | Has Policy | Has Procedure | Planned Document |
|---------|-------------|:----------:|:-------------:|-----------------|
| DE.CM-01.01 | Network/service monitoring for adverse events | Yes | No | #7 Monitoring & Detection |
| DE.CM-02.01 | Physical environment monitoring | Yes | No | #7 Monitoring & Detection |
| DE.CM-03.01 | Personnel activity/technology usage monitoring | No | No | #7 Monitoring & Detection |
| DE.CM-06.01 | External service provider monitoring | No | No | #7 Monitoring & Detection |
| DE.CM-09.01 | Computing HW/SW/runtime monitoring | No | No | #7 Monitoring & Detection |
| DE.AE-02.01 | Adverse event analysis | Yes | No | #7 Monitoring & Detection |
| DE.AE-03.01 | Multi-source information correlation | Yes | No | #7 Monitoring & Detection |
| DE.AE-04.01 | Adverse event impact/scope estimation | No | No | #7 Monitoring & Detection |
| DE.AE-06.01 | Adverse event info provided to authorized staff | No | No | #7 Monitoring & Detection |
| DE.AE-07.01 | Threat intelligence integration into analysis | No | No | #7 Monitoring & Detection |
| DE.AE-08.01 | Incident declaration based on defined criteria | No | No | #7 Monitoring & Detection |

### RESPOND (RS) — 13 controls

| Control | Description | Has Policy | Has Procedure | Planned Document |
|---------|-------------|:----------:|:-------------:|-----------------|
| RS.MA-01.01 | Incident response plan executed with third parties | No | No | #4 Incident Response |
| RS.MA-02.01 | Incident reports triaged and validated | No | No | #4 Incident Response |
| RS.MA-03.01 | Incidents categorized and prioritized | No | No | #4 Incident Response |
| RS.MA-04.01 | Incidents escalated or elevated as needed | No | No | #4 Incident Response |
| RS.MA-05.01 | Recovery initiation criteria applied | No | No | #4 Incident Response |
| RS.AN-03.01 | Root cause analysis performed | No | No | #4 Incident Response |
| RS.AN-06.01 | Investigation actions recorded with integrity | No | No | #4 Incident Response |
| RS.AN-07.01 | Incident data/metadata collected with integrity | No | No | #4 Incident Response |
| RS.AN-08.01 | Incident magnitude estimated and validated | No | No | #4 Incident Response |
| RS.CO-02.01 | Stakeholders notified of incidents | No | No | #4 Incident Response |
| RS.CO-03.01 | Information shared with designated stakeholders | No | No | #4 Incident Response |
| RS.MI-01.01 | Incidents contained | Yes | No | #4 Incident Response |
| RS.MI-02.01 | Incidents eradicated | Yes | No | #4 Incident Response |

### RECOVER (RC) — 8 controls

| Control | Description | Has Policy | Has Procedure | Planned Document |
|---------|-------------|:----------:|:-------------:|-----------------|
| RC.RP-01.01 | Recovery plan executed from incident response | No | No | #11 Recovery Plan |
| RC.RP-02.01 | Recovery actions selected and prioritized | No | No | #11 Recovery Plan |
| RC.RP-03.01 | Backup/restoration asset integrity verified | No | No | #11 Recovery Plan |
| RC.RP-04.01 | Post-incident operational norms established | No | No | #11 Recovery Plan |
| RC.RP-05.01 | Restored asset integrity verified, services restored | No | No | #11 Recovery Plan |
| RC.RP-06.01 | Recovery end declared, documentation completed | No | No | #11 Recovery Plan |
| RC.CO-03.01 | Recovery progress communicated to stakeholders | Yes | No | #11 Recovery Plan |
| RC.CO-04.01 | Public updates on recovery shared via approved methods | No | No | #11 Recovery Plan |

---

## HPC-Specific Considerations (NIST SP 800-223 / SP 800-234)

NIST has published HPC-specific security guidance that should inform our policies and procedures.

### Four-Zone Reference Architecture (SP 800-223)

| Zone | Description | DSI Equivalent | Key Threats |
|------|-------------|----------------|-------------|
| Access Zone | User entry point | SSH gateway, login nodes | Credential harvesting, brute force |
| Management Zone | Admin and control plane | SLURM controller, config management | Privilege escalation, config tampering |
| Computing Zone | Job execution | 32 GPU/CPU nodes | Side-channel attacks, cryptomining, data leakage between users |
| Data Storage Zone | Storage and archival | /home, /project, /scratch (2.1 PB) | Unauthorized access, data exfiltration |

### HPC-Specific Risks to Address in Documentation

- **Multi-tenancy:** OverSubscribe policy means multiple jobs share GPU nodes — risk of data leakage between users
- **Extended sessions:** 12-hour job limit increases credential exposure window
- **Compute node isolation:** Compute nodes should not be directly routable from outside the cluster
- **Performance vs. security tradeoffs:** Full-disk encryption and endpoint agents typical in enterprise IT may be inappropriate for compute nodes
- **Centralized logging:** Best practice is a syslog server on an isolated system for forensic baseline

---

## Open Questions (Decisions Needed Before Implementation)

1. **Where should documents live?** Options:
   - (a) New pages in `_policies/` on the Jekyll site
   - (b) Google Docs (matching the 15 existing policies)
   - (c) Both — policies in Google Docs, procedures on the site
   - (d) All in the repo as markdown files under `Policies and Procedures/`

2. ~~**Existing Google Docs policies:** Can we access and review the 15 existing policy documents to ensure procedures align with them?~~ **PARTIALLY RESOLVED (2026-04-01):** All 15 CS policy documents have been downloaded from Google Docs and are queued for individual review (see "CS Policy Evaluation Queue" section). Each must be approved before being added to the Jekyll site.

3. **Network diagram (ID.AM-03):** Do we have an existing diagram, or must one be created from scratch? What is the actual network topology (VLANs, switches, firewall rules, external connectivity)?

4. **Monitoring tools in use:** What specific tools are currently deployed? (e.g., Nagios, Prometheus, Grafana, SLURM accounting, syslog, UChicago central SIEM integration)

5. **Backup details:** What is the actual backup system? Frequency, retention, offsite copies, restore testing history?

6. **UChicago central IT relationship:** Which controls does UChicago's central IT/Information Assurance handle vs. DSI? (e.g., network firewall, SIEM, vulnerability scanning, physical data center security)

7. **Configuration management:** Is Ansible/Puppet/Salt or similar used? Are configs version-controlled?

8. **Timeline:** When is the next SFA assessment due?

---

## References

- [NIST CSF 2.0 (NIST.CSWP.29)](https://nvlpubs.nist.gov/nistpubs/CSWP/NIST.CSWP.29.pdf)
- [NIST SP 1302 — Quick-Start Guide for CSF Tiers](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.1302.pdf)
- [NIST SP 800-223 — HPC Security: Architecture, Threat Analysis, and Security Posture](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-223.pdf)
- [NIST SP 800-234 (Draft) — HPC Security Overlay](https://csrc.nist.gov/pubs/sp/800/234/ipd)
- [UChicago SFA FAQ](https://uchicago.service-now.com/it?id=kb_article_view&sysparm_article=KB06001871)
- SFA control tracker: `Policies and Procedures/SFA links to documentation - SFA Documentation questions.csv`
