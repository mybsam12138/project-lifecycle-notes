# PAS Motor Insurance Premium Overcharge — Incident Report & Root Cause Analysis

---

## Incident Summary

| Field | Detail |
|---|---|
| **Incident ID** | INC-2024-00892 |
| **Incident Title** | Premium Overcharge Due to Incorrect NCD Rate Lookup in PAS Motor Rating Engine |
| **System Affected** | Policy Administration System (PAS) — Motor Premium Rating Module |
| **Discovery Date** | 15 March 2024 |
| **Incident Period** | 01 January 2024 – 15 March 2024 (74 days) |
| **Severity** | High |
| **Priority** | P1 |
| **Defect Type** | Data / Logic Defect — Production |
| **Status** | Resolved |
| **Report Prepared By** | PAS System Team |
| **Report Date** | 30 March 2024 |

---

## 1. Incident Description

### Background — Motor Premium Calculation

In motor insurance (General Insurance), the annual premium is calculated using the following formula:

```
Gross Premium = Basic Premium × (1 - NCD%) + Loadings - Discounts

Where:
Basic Premium  = Vehicle Sum Insured × Tariff Rate (by vehicle category)
NCD%           = No Claim Discount — reward for claim-free years
                 (0%, 25%, 30%, 38.33%, 45%, 55%)
Loadings       = Additional charges (windscreen, extra driver, special perils)
Discounts      = Loyalty discount, anti-theft device discount, etc.
```

> **NCD (No Claim Discount)** is a critical component — the higher the NCD%, the lower the premium.
> A policyholder with 5 consecutive claim-free years is entitled to 55% NCD.

---

### What Happened

On 15 March 2024, the Finance team identified a premium collection anomaly during monthly reconciliation. Upon investigation, it was confirmed that the PAS Motor Premium Rating Module was **not applying the correct NCD percentage** to motor policies renewed from 01 January 2024 onwards.

The system was applying **0% NCD (no discount)** to all motor policy renewals, regardless of the policyholder's actual NCD entitlement. This caused all affected policyholders to be overcharged — they did not receive the NCD discount they were rightfully entitled to.

---

### What Should Have Happened

The PAS system should have:
1. Retrieved the policyholder's NCD entitlement from the Motor NCD database (Persatuan Insurans Am Malaysia — PIAM NCD system)
2. Mapped the correct NCD percentage based on the number of claim-free years
3. Applied the NCD discount in the premium calculation formula
4. Issued the renewal notice with the correct discounted premium

---

### What Actually Happened

The PAS system was:
1. Successfully retrieving the policyholder's NCD entitlement from PIAM NCD system
2. **Failing to pass the NCD value into the rating engine** due to a broken parameter mapping introduced in PAS Release v5.1.0
3. Defaulting to **0% NCD** when no value was received by the rating engine
4. Calculating and collecting premium without any NCD discount applied

---

## 2. Impact Assessment

### Financial Impact

| Metric | Detail |
|---|---|
| **Total Policies Affected** | 3,847 policies |
| **Product Affected** | Private Motor Comprehensive — Annual Renewal |
| **Incident Period** | 01 Jan 2024 – 15 Mar 2024 |
| **Average Vehicle Sum Insured** | RM 75,000 |
| **Average NCD Entitlement** | 38.33% |
| **Average Overcharge Per Policy** | RM 680.00 |
| **Total Overcharge Amount** | RM 2,615,960.00 |
| **Interest Applicable** | RM 21,799.67 (at BNM reference rate) |
| **Total Refund Liability** | RM 2,637,759.67 |

---

### NCD Entitlement Table (PIAM Standard)

| Claim-Free Years | NCD Entitlement | Effect on Premium |
|---|---|---|
| 0 years (new / after claim) | 0% | Full premium — no discount |
| 1 year | 25% | 25% off basic premium |
| 2 years | 30% | 30% off basic premium |
| 3 years | 38.33% | 38.33% off basic premium |
| 4 years | 45% | 45% off basic premium |
| 5 years and above | 55% | 55% off basic premium |

---

### Individual Policy Example

```
Policyholder       : Raj Kumar a/l Subramaniam
Policy No          : MTR-2024-008821
Vehicle            : Toyota Camry 2.5 (2020)
Vehicle Sum Insured: RM 85,000
NCD Entitlement    : 55% (5+ claim-free years)
Policy Type        : Comprehensive Motor

Basic Premium      = RM 85,000 × 2.63% (tariff rate)   = RM 2,235.50
NCD Discount       = RM 2,235.50 × 55%                  = RM 1,229.53

Correct Premium    = RM 2,235.50 – RM 1,229.53          = RM 1,005.97
Charged Premium    = RM 2,235.50 – RM 0.00 (0% NCD)     = RM 2,235.50
Overcharge         = RM 2,235.50 – RM 1,005.97          = RM 1,229.53
Total Refund Due   = RM 1,229.53
```

---

### Customer Impact by NCD Tier

| NCD Tier | Policies Affected | Avg Overcharge Per Policy |
|---|---|---|
| 25% NCD | 412 policies | RM 285.00 |
| 30% NCD | 689 policies | RM 342.00 |
| 38.33% NCD | 1,124 policies | RM 580.00 |
| 45% NCD | 876 policies | RM 720.00 |
| 55% NCD | 746 policies | RM 1,150.00 |
| **Total** | **3,847 policies** | **Avg RM 680.00** |

> **Note:** Policyholders with 0% NCD entitlement were not affected — the system defaulted to 0%, which matched their actual entitlement.

---

### Regulatory Impact

| Assessment | Detail |
|---|---|
| **Reportable to BNM?** | Yes — financial impact of RM 2.6M exceeds materiality threshold |
| **Reportable to PIAM?** | Yes — NCD is a PIAM-regulated entitlement, deviation must be reported |
| **Consumer Protection Breach?** | Yes — policyholders denied their entitled NCD discount |
| **Solvency Impact?** | Low — refund liability manageable within operational reserves |
| **Reputational Risk?** | High — NCD is well-known to motor policyholders, overcharge likely to generate complaints |

---

## 3. Timeline of Events

| Date | Event |
|---|---|
| **28 Dec 2023** | PAS Release v5.1.0 deployed to production — included motor renewal workflow enhancement and PIAM NCD API integration upgrade from v2 to v3 |
| **01 Jan 2024** | Motor renewal cycle begins for January renewals — overcharge starts silently |
| **03 Jan 2024** | First batch of 312 motor renewal notices issued — all with 0% NCD applied |
| **15 Jan 2024** | 2 customer complaints received via call centre — customers querying why premium is higher than previous year. Logged as general enquiry, not escalated |
| **28 Jan 2024** | Second batch of renewals processed — further 890 policies affected |
| **01 Feb 2024** | Customer complaints increase to 18 — call centre still handling individually, no pattern flagged |
| **15 Mar 2024** | Finance team identifies premium collection significantly higher than forecast during monthly reconciliation — escalates to PAS team |
| **15 Mar 2024** | PAS team investigates — discovers NCD parameter not being passed to rating engine |
| **16 Mar 2024** | Root cause confirmed — broken parameter mapping in PIAM NCD API v3 integration |
| **16 Mar 2024** | P1 incident declared — Management, IT, Finance, Underwriting, Compliance, Legal notified |
| **16 Mar 2024** | Motor renewal processing temporarily suspended |
| **17 Mar 2024** | Full impact assessment completed — 3,847 policies, RM 2,637,759.67 total refund liability |
| **18 Mar 2024** | BNM notified via official incident report |
| **18 Mar 2024** | PIAM notified — NCD entitlement deviation reported |
| **19 Mar 2024** | Hotfix developed and tested in UAT environment |
| **20 Mar 2024** | Underwriting team validates corrected NCD premium output across all NCD tiers |
| **21 Mar 2024** | Hotfix deployed to production — PAS Release v5.1.0-HF1 |
| **21 Mar 2024** | Motor renewal processing resumed with correct NCD applied |
| **25 Mar 2024** | Customer notification letters and revised renewal certificates dispatched to all 3,847 policyholders |
| **28 Mar 2024** | First batch of refunds processed — 1,200 policies |
| **05 Apr 2024** | Second batch of refunds processed — 1,500 policies |
| **12 Apr 2024** | Third batch of refunds processed — 1,147 policies |
| **15 Apr 2024** | All 3,847 refunds confirmed completed |
| **30 Apr 2024** | Full remediation report submitted to BNM and PIAM |
| **30 Apr 2024** | Incident formally closed |

---

## 4. Root Cause Analysis

### 4.1 Technical Root Cause

The PAS Motor Rating Engine receives vehicle and policyholder data from the Renewal Processing Module via an internal API call. One of the parameters passed is the **NCD percentage value** retrieved from the PIAM NCD system.

In PAS Release v5.1.0, the PIAM NCD API integration was upgraded from API version v2 to v3 to support the new PIAM NCD data structure. During this upgrade, the **parameter name for NCD percentage was renamed** in the API response:

| | Old API (v2) | New API (v3) |
|---|---|---|
| NCD Parameter Name | `ncd_rate` | `ncd_percentage` |

The developer updated the PIAM NCD API call to v3, but **did not update the parameter mapping** in the Renewal Processing Module that reads the API response and passes it to the rating engine. As a result:

- The system called PIAM NCD API v3 successfully and received the correct NCD value
- But the Renewal Processing Module was still reading `ncd_rate` (old v2 parameter name)
- Since `ncd_rate` no longer existed in the v3 response, the value returned was **NULL**
- The rating engine received NULL and defaulted to **0% NCD**

**Defective Code Logic:**
```
// PIAM NCD API v3 called successfully
apiResponse = callPIAMNcdAPI(vehicleNo, icNo)

// WRONG — reading old v2 parameter name (no longer exists in v3 response)
ncdValue = apiResponse.ncd_rate        ← returns NULL

// Rating engine receives NULL, silently defaults to 0%
if ncdValue == NULL then ncdValue = 0

premium = basicPremium × (1 - ncdValue)   ← 0% NCD applied to all renewals
```

**Correct Code Logic:**
```
// PIAM NCD API v3 called successfully
apiResponse = callPIAMNcdAPI(vehicleNo, icNo)

// CORRECT — reading updated v3 parameter name
ncdValue = apiResponse.ncd_percentage     ← returns correct NCD e.g. 0.55

// Validation — never silently default NCD to 0 on a renewal policy
if ncdValue == NULL then raiseError("NCD value missing — halt renewal processing")

premium = basicPremium × (1 - ncdValue)   ← correct NCD applied
```

---

### 4.2 Contributing Factors

| Factor | Detail |
|---|---|
| **Incomplete API migration** | Developer updated the API call to v3 but did not update the response parameter mapping — partial migration |
| **No API contract validation** | No automated check to verify all expected parameters are present in the API response after the integration upgrade |
| **Silent NULL defaulting** | System defaulted NULL NCD to 0% without raising any warning, alert, or error log |
| **Insufficient integration testing** | Integration test only verified API connectivity — did not assert that the correct NCD value flowed through to the premium calculation |
| **No NCD sanity check in rating engine** | Rating engine accepted 0% NCD on renewal policies without any validation — renewal policies should rarely have 0% NCD unless after a claim |
| **Customer complaints not escalated** | Early complaints in January handled individually by call centre — no pattern recognition or escalation to flag potential systemic issue |
| **No post-deployment premium monitoring** | No automated comparison of premium collected vs expected premium by NCD tier after deployment |

---

### 4.3 Why Was It Not Caught Earlier?

| Stage | Why Missed |
|---|---|
| **Development** | Developer only updated API call — assumed response mapping was unchanged, did not verify end-to-end parameter flow |
| **Code Review** | Reviewer checked API upgrade logic but did not trace parameter mapping through to rating engine input |
| **Integration Testing** | Test verified API connectivity and response received — did not assert NCD value in premium calculation output |
| **UAT** | UAT test scenarios used new business policies (0% NCD) — no renewal test cases with NCD > 0% |
| **Post Go-Live** | No automated premium monitoring — early customer complaints treated as isolated cases, not as a systemic pattern |
| **Call Centre** | Complaints in January and February not aggregated or escalated as a potential systemic issue |

---

## 5. Resolution

### 5.1 Immediate Fix

| Action | Detail |
|---|---|
| **Code fix** | Updated parameter mapping in Renewal Processing Module from `ncd_rate` to `ncd_percentage` to align with PIAM NCD API v3 response |
| **NULL handling added** | Rating engine now raises error and halts renewal processing if NCD value is NULL — no silent defaulting to 0% |
| **Fix verified by** | Senior Developer + Underwriting Team |
| **Testing** | Full regression test across all NCD tiers (0%, 25%, 30%, 38.33%, 45%, 55%) × vehicle categories × sum insured bands |
| **Deployment** | Emergency hotfix — PAS Release v5.1.0-HF1 deployed 21 March 2024 |
| **Post-deployment validation** | Underwriting team validated 50 sample renewal premiums across all NCD tiers — all confirmed correct |

---

### 5.2 Data Remediation

| Action | Detail |
|---|---|
| **Identify affected policies** | PAS batch query — all motor comprehensive renewal policies processed 01 Jan 2024 – 15 Mar 2024 with NCD entitlement > 0% |
| **Retrieve correct NCD entitlement** | Re-queried PIAM NCD system for all 3,847 affected policies to get correct NCD% per policy |
| **Recalculate correct premium** | Rating engine re-run with correct NCD values for all affected policies |
| **Calculate refund per policy** | Finance computed overcharge amount + interest for each policy |
| **Update PAS records** | Premium Correction Endorsement raised for each affected policy |
| **Issue revised renewal certificate** | New motor certificate issued to each affected policyholder with correct premium and NCD |
| **Refund processing** | Bank transfer refunds processed in 3 batches — 28 Mar, 05 Apr, 12 Apr 2024 |
| **Refund completion** | All 3,847 refunds confirmed received by 15 April 2024 |

---

### 5.3 Customer Notification

Formal correction letter and revised motor renewal certificate dispatched to all 3,847 policyholders on 25 March 2024.

Letter included:
- Explanation that a system error caused NCD discount to not be applied correctly
- Policyholder's correct NCD entitlement (e.g. 55%)
- Correct premium amount vs amount charged
- Total overcharge amount and refund amount
- Refund method (bank transfer) and expected timeline (within 14 working days)
- Apology for the inconvenience caused
- Dedicated helpline — 0800-XXX-XXXX

Total customer complaints received post-notification: 124 — all resolved within 3 working days.

---

### 5.4 Policy Endorsement & Certificate Reissuance

- Endorsement type: **Premium Correction Endorsement — NCD Reinstatement**
- All 3,847 motor policies updated in PAS:
  - Correct NCD percentage reinstated
  - Correct premium amount recorded
  - Endorsement backdated to policy renewal date
  - Endorsement reason: System error — NCD discount not applied due to API parameter mapping defect
- **Revised motor certificate (Cover Note) reissued** to all affected policyholders
- Original certificate superseded and marked cancelled in PAS
- JPJ (Road Transport Department) database updated with correct premium and NCD details
- No policy cancellation and reissuance — existing policy retained
- No new underwriting required — vehicle risk and coverage unchanged

---

## 6. Regulatory Reporting

### BNM Notification

| Item | Detail |
|---|---|
| **Notified to** | Bank Negara Malaysia (BNM) |
| **Notification date** | 18 March 2024 |
| **Notification method** | Official incident report via BNM regulatory portal |
| **BNM reference** | BNM-INC-2024-1847 |
| **BNM response** | Acknowledged — requested full remediation report by 30 April 2024 |

### PIAM Notification

| Item | Detail |
|---|---|
| **Notified to** | Persatuan Insurans Am Malaysia (PIAM) |
| **Notification date** | 18 March 2024 |
| **Reason** | NCD is a PIAM-regulated entitlement — any deviation from the NCD schedule must be reported to PIAM |
| **PIAM response** | Acknowledged — required confirmation that all affected policyholders receive correct NCD reinstatement and refund |

### Regulatory Report Content Submitted

- Incident description and full timeline
- Root cause — API parameter mapping defect in PIAM NCD v3 integration
- Number of policies affected and total financial exposure
- NCD reinstatement confirmation for all 3,847 affected policies
- Refund plan — method, timeline, and completion confirmation
- Customer notification approach and response
- Preventive measures and control enhancements going forward

---

## 7. Preventive Measures

### 7.1 Technical Controls

| Control | Detail | Target Date |
|---|---|---|
| API contract validation | Automated check after every API upgrade — verify all expected parameters are present and non-null in response | 30 Apr 2024 |
| NCD NULL validation in rating engine | Rating engine raises error and halts if NCD value is NULL on a renewal policy — no silent defaulting | 22 Mar 2024 |
| Automated NCD premium regression test | Test suite covering all NCD tiers × vehicle categories × sum insured bands — mandatory before every motor PAS release | 30 Apr 2024 |
| API version change classified as high-risk | Any API version upgrade classified as Tier 1 change — requires end-to-end integration testing and senior developer review | 01 Apr 2024 |

### 7.2 Process Controls

| Control | Detail | Target Date |
|---|---|---|
| Mandatory underwriting UAT sign-off | Underwriting team must validate NCD premium output for every motor-related PAS release | 01 Apr 2024 |
| Motor renewal test cases with NCD > 0% | QA test library updated — all motor renewal scenarios must include policies across all NCD tiers | 30 Apr 2024 |
| Call centre complaint pattern monitoring | Call centre to flag and escalate if more than 5 similar premium complaints received within 7 days | 01 Apr 2024 |
| Post-deployment premium spot check | Underwriting spot check of 30 sample renewal policies within 48 hours of every production deployment | 22 Mar 2024 |

### 7.3 Monitoring Controls

| Control | Detail | Target Date |
|---|---|---|
| NCD distribution monitoring report | Weekly report comparing NCD distribution of new renewals vs historical baseline — alert if 0% NCD proportion abnormally high | 15 Apr 2024 |
| Premium variance alert | Automated alert if average motor renewal premium deviates more than 5% from same period prior year | 15 Apr 2024 |
| PIAM NCD API health check | Daily automated check to verify PIAM NCD API response contains all required parameters and returns valid NCD values | 15 Apr 2024 |

---

## 8. Lessons Learned

| Lesson | Action Taken |
|---|---|
| API version upgrades must include full end-to-end parameter mapping validation, not just connectivity testing | API upgrade checklist created — mandatory end-to-end parameter tracing required before release |
| System must never silently default critical values like NCD to zero — must raise error and halt | NULL handling policy updated — all critical rating parameters must trigger error on NULL |
| UAT must include motor renewal scenarios with NCD > 0%, not just new business policies | Motor UAT test library overhauled to cover all NCD tiers |
| Call centre complaints are an early warning signal — must be aggregated and monitored for patterns | Call centre complaint monitoring dashboard implemented — auto-escalation threshold configured |
| Post-deployment premium monitoring is essential for financial calculation systems | NCD distribution report and premium variance alert implemented as standard BAU activity |
| PIAM NCD API changes must be treated as high-risk integration changes requiring joint review | Classified as Tier 1 change — requires underwriting, IT, and compliance joint review gate |

---

## 9. Incident Closure

| Item | Detail |
|---|---|
| **System hotfix deployed** | ✅ 21 March 2024 |
| **NCD reinstated for all affected policies** | ✅ 28 March 2024 |
| **Revised motor certificates reissued** | ✅ 28 March 2024 |
| **JPJ database updated** | ✅ 28 March 2024 |
| **Customer notifications dispatched** | ✅ 25 March 2024 |
| **All refunds completed** | ✅ 15 April 2024 |
| **BNM notified** | ✅ 18 March 2024 |
| **PIAM notified** | ✅ 18 March 2024 |
| **BNM & PIAM remediation report submitted** | ✅ 30 April 2024 |
| **Preventive measures implemented** | ✅ 15 April 2024 |
| **Incident formally closed** | ✅ 30 April 2024 |

---

*Document Classification: Internal — Confidential*
*Prepared by: PAS System Team | Reviewed by: Head of Operations, Head of Underwriting (Motor), Head of Compliance*
