# Incident Report — Complete Guide

---

## 1. What is an Incident Report?

An Incident Report is a **formal document** that records the details of an unexpected event, system failure, or operational issue that has caused or has the potential to cause harm to:

- Customers / Policyholders
- Business operations
- Financial integrity
- Regulatory compliance

It is **not** a root cause analysis (RCA) — it is the **first formal record** that an incident has occurred, capturing what is known at that point in time.

> **Key Principle:** An Incident Report is a **living document**. It does not need to have all the answers at the time of filing. "We are investigating" is a perfectly acceptable status.

---

## 2. Timing — When Should It Be Filed?

| Report Type | Timing | Wait for Resolution? |
|---|---|---|
| **Incident Report** | Within 24–72 hours of discovery | ❌ No — file immediately |
| **Root Cause Analysis (RCA)** | Within 5–30 days after investigation | ❌ No — file after root cause confirmed |
| **Closure Report** | After full resolution | ✅ Yes — only this waits |

### Timeline Flow:
```
Incident Discovered
        ↓
24–72 hrs → Incident Report Filed
        ↓
5–30 days → RCA Report Completed
        ↓
Fix Developed & Deployed
        ↓
Refunds / Remediation Completed
        ↓
Closure Report Filed → Incident Closed
```

---

## 3. What Should an Incident Report Contain?

### Section 1: Basic Information (Header)

| Field | Details |
|---|---|
| Incident ID | Unique reference number (e.g. INC-2024-0315-001) |
| Report Date | Date incident report is filed |
| Incident Date | Date incident occurred / was discovered |
| Prepared By | Name and team |
| Reviewed By | Manager / Head of Department |
| Classification | System Error / Human Error / External |
| Severity Level | Critical / High / Medium / Low |

---

### Section 2: Incident Description

Answer the 5W1H:

- **What** happened?
- **Where** did it happen? (system, module, environment)
- **When** did it start? When was it discovered?
- **Who** is affected? (customers, teams)
- **Why** did it happen? (preliminary only)
- **How** was it discovered?

---

### Section 3: Impact Assessment

| Category | Details |
|---|---|
| Number of records/policies affected | Exact count or estimated range |
| Period affected | Start date to discovery date |
| Financial impact | Amount overcharged / at risk |
| Customer impact | Nature of harm to customers |
| Regulatory impact | Any compliance / consumer protection breach |
| Reputational impact | Low / Medium / High |

---

### Section 4: Incident Timeline

Chronological list of key events from when the issue started to the point of reporting.

---

### Section 5: Immediate Actions Taken

List all containment actions already performed at the time of filing:

- Who was notified
- Whether the issue has been stopped from continuing
- Whether affected records have been identified
- Whether any workaround is in place

---

### Section 6: Preliminary Root Cause

Note: This is preliminary. Full RCA is a separate document.

State what is currently known or suspected about the cause of the incident.

---

### Section 7: Containment Measures

| Action | Status | Owner | Target Date |
|---|---|---|---|
| Stop further impact | Done / In Progress | Team | Date |
| Identify affected records | Done / In Progress | Team | Date |
| Calculate financial impact | Done / In Progress | Team | Date |
| Develop fix | In Progress | IT | Date |

---

### Section 8: Notification Status

| Stakeholder | Notified? | Date | Method |
|---|---|---|---|
| Senior Management | Yes / No | | Email / Meeting |
| Compliance Team | Yes / No | | Email |
| Legal Team | Yes / No | | Email |
| Regulator | Pending / Yes / No | | Formal Letter |
| Affected Customers | Pending / Yes / No | | Letter / Email |

---

### Section 9: Next Steps

Numbered list of actions to be taken after the incident report is filed, with owners and target dates.

---

### Section 10: Sign Off

| Role | Name | Date |
|---|---|---|
| Prepared By | | |
| Reviewed By | | |
| Approved By | | |
| Compliance Sign Off | | |

---

## 4. Worked Example — NCD Rating Error

---

### INCIDENT REPORT

**Incident ID:** INC-2024-0315-001  
**Report Date:** 15 March 2024  
**Incident Start Date:** 01 January 2024  
**Prepared By:** Finance Team  
**Reviewed By:** Head of Finance  
**Classification:** Production System Defect  
**Severity Level:** High  

---

### 2. Incident Description

**What:**  
The PAS Motor Premium Rating Module was not applying the correct NCD (No Claims Discount) percentage to motor policy renewals. Policyholders were overcharged as their entitled NCD discount was not reflected in the premium calculation.

**Where:**  
PAS System — Motor Premium Rating Module (Production Environment)

**When:**  
- Issue started: 01 January 2024 (following deployment of PAS Release v5.1.0)  
- Issue discovered: 15 March 2024 (during monthly financial reconciliation)

**Who is affected:**  
All motor policyholders who renewed their policies between 01 January 2024 and 15 March 2024 with an NCD entitlement.

**How discovered:**  
Finance team identified a premium collection anomaly during the March 2024 monthly reconciliation exercise.

---

### 3. Impact Assessment

| Category | Details |
|---|---|
| Policies affected | TBC — investigation in progress |
| Period affected | 01 January 2024 — 15 March 2024 |
| Financial impact | TBC — estimated overcharge under investigation |
| Customer impact | Policyholders denied their entitled NCD discount |
| Regulatory impact | Potential Consumer Protection Breach |
| Reputational impact | Medium |

---

### 4. Incident Timeline

| Date | Event |
|---|---|
| 01 Jan 2024 | PAS Release v5.1.0 deployed to production |
| 01 Jan 2024 | Motor policy renewals begin processing without correct NCD |
| 15 Mar 2024 | Finance team identifies premium collection anomaly during reconciliation |
| 15 Mar 2024 | Investigation initiated; IT and Compliance teams notified |
| 15 Mar 2024 | Preliminary root cause identified — broken parameter mapping in v5.1.0 |

---

### 5. Immediate Actions Taken

- ✅ Senior Management notified on 15 March 2024
- ✅ Compliance and Legal teams notified on 15 March 2024
- ✅ IT team engaged to investigate PAS Rating Module
- ✅ Preliminary root cause identified
- ✅ New renewals placed under manual review pending system fix
- 🔄 Full list of affected policies being compiled
- 🔄 Refund calculation in progress

---

### 6. Preliminary Root Cause

PAS Release v5.1.0 introduced a broken parameter mapping in the Motor Premium Rating Module. This caused the NCD value retrieved from the PIAM NCD System to fail to be passed into the rating engine, resulting in renewals being calculated without the policyholder's entitled NCD discount.

*Note: Full Root Cause Analysis (RCA) report to follow.*

---

### 7. Containment Measures

| Action | Status | Owner | Target Date |
|---|---|---|---|
| Halt incorrect renewals / apply manual review | ✅ Done | Operations | 15 Mar 2024 |
| Identify all affected policies | 🔄 In Progress | Finance | 22 Mar 2024 |
| Calculate total overcharged amounts | 🔄 In Progress | Finance | 22 Mar 2024 |
| Develop system fix | 🔄 In Progress | IT | 29 Mar 2024 |
| Test and deploy fix | 🔄 Pending | IT | 05 Apr 2024 |
| Process refunds to affected policyholders | 🔄 Pending | Finance | 30 Apr 2024 |

---

### 8. Notification Status

| Stakeholder | Notified? | Date | Method |
|---|---|---|---|
| CEO / Senior Management | ✅ Yes | 15 Mar 2024 | Email |
| Compliance Team | ✅ Yes | 15 Mar 2024 | Email |
| Legal Team | ✅ Yes | 15 Mar 2024 | Email |
| Regulator (BNM) | 🔄 Pending | TBC | Formal Letter |
| Affected Policyholders | 🔄 Pending | TBC | Letter / Email |

---

### 9. Next Steps

1. Complete full affected policy identification — Finance Team by 22 Mar 2024
2. Finalize total financial impact calculation — Finance Team by 22 Mar 2024
3. Complete formal RCA Report — IT & Compliance by 29 Mar 2024
4. Develop, test and deploy system fix — IT Team by 05 Apr 2024
5. Assess regulatory notification requirement — Compliance by 22 Mar 2024
6. Notify affected policyholders — Customer Services by 30 Apr 2024
7. Process all refunds — Finance Team by 30 Apr 2024
8. File Closure Report upon full resolution

---

### 10. Sign Off

| Role | Name | Date |
|---|---|---|
| Prepared By | | 15 Mar 2024 |
| Reviewed By | | 15 Mar 2024 |
| Approved By | | 15 Mar 2024 |
| Compliance Sign Off | | 15 Mar 2024 |

---

## 5. Key Principles Summary

| Principle | Why It Matters |
|---|---|
| **File immediately** | Do not wait for all answers — speed shows transparency |
| **Be factual** | Only confirmed facts; clearly label what is preliminary |
| **Be complete** | All known information at time of filing |
| **Be honest** | Even if it reflects badly on the company |
| **Assign owners** | Every action must have a person responsible |
| **Keep it updated** | Incident Report is a living document until closure |
| **Separate from RCA** | Incident Report ≠ Root Cause Analysis |

---

*Document Version: 1.0*  
*Classification: Internal / Confidential*
