# Root Cause Analysis (RCA) Report — Guide

---

## 1. What is an RCA Report?

A Root Cause Analysis (RCA) Report identifies **why** an incident happened and **how to prevent it from recurring**.

> **Incident Report** = WHAT happened
> **RCA Report** = WHY it happened + HOW to prevent recurrence

---

## 2. Timing

| Phase | Timing |
|---|---|
| Investigation starts | Immediately after Incident Report filed |
| Root cause confirmed | 3–10 days typically |
| RCA report completed | 5–30 days after incident discovery |
| Fix deployment | After RCA confirmed |

```
Incident Report Filed
        ↓
Investigation (3–10 days)
        ↓
RCA Report Written ← HERE
        ↓
Fix Developed & Deployed
        ↓
Closure Report
```

> RCA does **NOT** wait for full resolution.
> Fix deployment happens **after** RCA is confirmed, not before.

---

## 3. What Should RCA Contain?

| Section | Content |
|---|---|
| **Incident Summary** | Brief recap of what happened |
| **Timeline** | Chronological events from cause to discovery |
| **Impact Summary** | Confirmed number of affected records and financial impact |
| **Root Cause** | Immediate cause, contributing factors, underlying root cause |
| **Why Not Caught Earlier** | Gaps in testing, monitoring, controls |
| **Corrective Actions** | Fix, preventive measures, owners and dates |
| **Lessons Learned** | Process improvements going forward |
| **Sign Off** | Tech Lead, Management, Compliance |

---

## 4. How to Find Root Cause — 5 Whys Method

```
Why 1: NCD not applied to renewals
Why 2: NCD value not passed to rating engine
Why 3: Parameter mapping broken in v5.1.0
Why 4: Code refactor removed the mapping line
Why 5: No unit test existed to catch missing NCD mapping

ROOT CAUSE:
Insufficient test coverage for NCD parameter mapping
```

---

## 5. Worked Example — NCD Rating Error

---

**RCA Report ID:** RCA-2024-0315-001
**Related Incident ID:** INC-2024-0315-001
**Report Date:** 29 March 2024
**Prepared By:** Tech Lead
**Approved By:** CTO

---

### Incident Summary

PAS v5.1.0 introduced a broken parameter mapping that caused NCD values retrieved from PIAM NCD System to not be passed into the rating engine. All motor renewals from 01 Jan 2024 were processed without NCD discount, resulting in overcharged premiums. Discovered by Finance team on 15 Mar 2024 during monthly reconciliation.

---

### Root Cause

| | Details |
|---|---|
| **Immediate Cause** | NCD parameter mapping line removed during code refactor in v5.1.0 |
| **Contributing Factor** | No unit test for NCD parameter; not in regression test scope |
| **Root Cause** | Code refactor was not validated against NCD flow; no test coverage to catch it |
| **Why Not Caught** | No unit test, no regression test, no production monitoring for NCD = 0 on renewals |

---

### Corrective Actions

| Action | Owner | Target Date |
|---|---|---|
| Restore NCD parameter mapping in code | Developer | 05 Apr 2024 |
| Add unit test for NCD parameter mapping | QA | 05 Apr 2024 |
| Add NCD flow to regression test checklist | QA Lead | 05 Apr 2024 |
| Add production alert for renewals with NCD = 0 | IT Ops | 12 Apr 2024 |

---

### Lessons Learned

- Code refactors must include validation of all parameter mappings
- NCD end-to-end flow must be part of every release regression test
- Production monitoring should alert on anomalous NCD = 0 patterns

---

*Document Version: 1.0 | Classification: Internal / Confidential*
