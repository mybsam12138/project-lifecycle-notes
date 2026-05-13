# Production Data Fix — Common Methods & Steps

---

## Overview — Common Methods

| Method | When to Use | Risk Level |
|---|---|---|
| **1. Manual UI (Policy by Policy)** | < 20 records | Low |
| **2. Batch Process via Application** | 20–10,000 records | Medium |
| **3. Direct Database Script** | Emergency only, no application method available | High |
| **4. Endorsement Auto-Batch** | Insurance policy correction with refund | Medium |

---

## Golden Rules Before Any Production Fix

```
STOP → BACKUP → FIX → VERIFY → RECOVER (if needed)
```

> Never run any fix directly on production without backup.
> Never fix without an approved rollback plan.
> Always add audit log for every record changed.

---

---

## Method 1: Manual UI (Policy by Policy)

### When to Use
- Small number of affected records (< 20)
- No batch capability available
- Fix requires human judgement per record

### Steps

```
Step 1: STOP
─────────────────────────────
□ Notify operations team to hold any new transactions
  on affected policy types during fix window
□ Get management approval to proceed

Step 2: BACKUP
─────────────────────────────
□ Export affected policy list to Excel/CSV
□ Screenshot or record current premium values
  before any change
□ DBA to backup related tables:
  e.g. POLICY, PREMIUM, ENDORSEMENT, NCD

Step 3: FIX
─────────────────────────────
□ Open each policy in application UI
□ Recalculate premium using corrected logic
□ Verify NCD value is now correctly applied
□ Generate endorsement for correction
□ Get supervisor approval on endorsement
□ Tick off policy on checklist

Step 4: AUDIT LOG
─────────────────────────────
□ Record in tracker for each policy:
  - Policy number
  - Original premium
  - Corrected premium
  - Difference (refund amount)
  - Endorsement number
  - Fixed by (staff name)
  - Fixed date/time

Step 5: VERIFY
─────────────────────────────
□ Second person to spot check each corrected policy
□ Reconcile total refund amount against expected total
□ Confirm endorsement created for every policy

Step 6: RECOVER (if error found)
─────────────────────────────
□ Identify which policies were incorrectly fixed
□ Raise reversal endorsement to undo incorrect change
□ Re-apply correct fix
□ Do NOT restore from DB backup unless critical corruption
```

---

---

## Method 2: Batch Process via Application

### When to Use
- Large number of affected records (20+)
- Repeatable, consistent fix needed
- Application exposes service/API methods to call

### Steps

```
Step 1: STOP
─────────────────────────────
□ Schedule fix during off-peak hours (e.g. midnight)
□ Notify all stakeholders of maintenance window
□ Disable affected module or put application
  in maintenance mode if possible
□ Prevent new transactions on affected policy types

Step 2: BACKUP
─────────────────────────────
□ DBA to backup all related tables BEFORE batch runs:
  e.g.
  BACKUP TABLE policy_premium AS policy_premium_bak_20240315;
  BACKUP TABLE endorsement AS endorsement_bak_20240315;
  BACKUP TABLE ncd_entitlement AS ncd_entitlement_bak_20240315;

□ Export full affected policy list to CSV as reference
□ Record row counts before fix:
  e.g. SELECT COUNT(*) FROM policy_premium WHERE affected = true;

Step 3: WRITE BATCH PROCESS
─────────────────────────────
□ Write batch as a CLIENT of the application
  — call existing service methods, do NOT bypass them
□ Batch logic:
  1. Query all affected policies
  2. For each policy:
     a. Retrieve correct NCD from PIAM system
     b. Call rating engine with correct NCD value
     c. Calculate correct premium
     d. Calculate refund difference
     e. Call endorsement service to create correction endorsement
     f. Write audit log entry
     g. Mark policy as fixed in tracking table
  3. On any error: log error, skip to next policy, continue
  4. Generate summary report at end of run

□ Example pseudocode:
─────────────────────────────
  affectedPolicies = db.query("SELECT * FROM policies WHERE affected = true AND fixed = false")

  for each policy in affectedPolicies:
    try:
      ncd = piamService.getNCD(policy.id)
      correctPremium = ratingEngine.calculate(policy, ncd)
      refundAmount = policy.paidPremium - correctPremium
      endorsement = endorsementService.createCorrection(policy, correctPremium, refundAmount)
      auditLog.write(policy.id, original, correctPremium, refundAmount, endorsement.id)
      db.update(policy.id, fixed = true)
    catch error:
      errorLog.write(policy.id, error)
      continue

  report.generate(totalFixed, totalErrors, totalRefundAmount)
─────────────────────────────

Step 4: TEST BEFORE PRODUCTION
─────────────────────────────
□ Run batch on UAT/staging environment first
□ Verify endorsements created correctly
□ Verify NCD correctly applied
□ Verify refund amounts correct
□ Verify audit log entries complete
□ Get tech lead and business sign off on UAT results

Step 5: RUN IN PRODUCTION
─────────────────────────────
□ Run batch on small subset first (e.g. 5 policies)
□ Manually verify those 5 before proceeding
□ If OK, run full batch
□ Monitor logs in real time during run
□ Keep DBA on standby during run

Step 6: AUDIT LOG
─────────────────────────────
Each record in audit log must capture:
□ Policy number
□ Run timestamp
□ Original premium
□ Corrected premium
□ NCD value applied
□ Refund amount
□ Endorsement number generated
□ Status (success / error)
□ Batch run ID

Step 7: VERIFY
─────────────────────────────
□ Count fixed policies = count of affected policies
□ Sum of refund amounts = expected total
□ Zero policies in error log (or investigate each error)
□ All endorsements approved in system
□ Finance team confirms refund file received

Step 8: RECOVER (if batch error)
─────────────────────────────
□ Stop batch immediately if critical error found
□ Identify policies already fixed vs not yet fixed
  (use fixed = true/false flag in tracking table)
□ For incorrectly fixed policies:
  — Restore from backup table:
    INSERT INTO policy_premium
    SELECT * FROM policy_premium_bak_20240315
    WHERE policy_id IN (affected list)
□ Investigate error, fix batch code
□ Re-run batch only on unfixed policies
  (fixed = false filter prevents double processing)
```

---

---

## Method 3: Direct Database Script

### When to Use
- Emergency only
- Application method not available
- Approved by DBA + Management + Compliance

> ⚠️ HIGH RISK — Last resort only
> Never use this as first option

### Steps

```
Step 1: STOP
─────────────────────────────
□ Stop application completely
□ Block all user access during fix
□ Get written approval from Management + DBA + Compliance

Step 2: BACKUP
─────────────────────────────
□ Full database backup before any script runs
□ Backup specific affected tables separately:
  CREATE TABLE policy_premium_bak_20240315
  AS SELECT * FROM policy_premium;

□ Verify backup is restorable before proceeding

Step 3: FIX
─────────────────────────────
□ Write SQL script with transaction control:

  BEGIN TRANSACTION;

  -- Update premium values
  UPDATE policy_premium
  SET premium_amount = correct_amount,
      ncd_percentage = correct_ncd,
      updated_by = 'BATCH_FIX_20240315',
      updated_date = SYSDATE
  WHERE policy_id IN (SELECT policy_id FROM affected_policies);

  -- Verify row count before commit
  -- Expected: XX rows
  SELECT COUNT(*) FROM policy_premium WHERE updated_by = 'BATCH_FIX_20240315';

  -- If count correct:
  COMMIT;

  -- If count wrong:
  ROLLBACK;

□ Always run SELECT first to verify before UPDATE
□ Always use transaction — never auto-commit

Step 4: AUDIT LOG
─────────────────────────────
□ Insert audit records for every row changed:
  INSERT INTO audit_log
  (policy_id, action, old_value, new_value, changed_by, changed_date)
  SELECT policy_id, 'PREMIUM_CORRECTION',
         old_premium, new_premium,
         'BATCH_FIX_20240315', SYSDATE
  FROM affected_policies;

Step 5: VERIFY
─────────────────────────────
□ Run reconciliation query to confirm all values correct
□ Row counts match expected
□ No unexpected records changed
□ Audit log entries complete

Step 6: RECOVER
─────────────────────────────
□ If error found after commit:
  INSERT INTO policy_premium
  SELECT * FROM policy_premium_bak_20240315
  WHERE policy_id IN (affected list);

□ If error found before commit:
  ROLLBACK; — transaction handles it automatically
```

---

---

## Method 4: Endorsement Auto-Batch (Insurance Specific)

### When to Use
- Insurance policy premium correction
- Refund needs to be generated
- Proper audit trail via endorsement required

### Steps

```
Step 1: STOP
─────────────────────────────
□ Freeze affected policy renewals
□ Maintenance window agreed with business

Step 2: BACKUP
─────────────────────────────
□ Backup POLICY, PREMIUM, ENDORSEMENT, NCD tables
□ Export affected policy list

Step 3: FIX
─────────────────────────────
□ Batch calls application endorsement service:
  For each affected policy:
  1. Retrieve correct NCD from PIAM
  2. Recalculate correct premium via rating engine
  3. Create correction endorsement
  4. Endorsement captures:
     - Original premium
     - Corrected premium
     - Refund amount
     - Reason: "NCD Parameter Mapping Error — PAS v5.1.0"
  5. Auto-approve endorsement (if authorised)
  6. Generate refund instruction to Finance system

Step 4: AUDIT LOG
─────────────────────────────
□ Endorsement itself IS the audit record
□ Additional batch log:
  - Policy number
  - Endorsement number
  - Original premium
  - Corrected premium
  - Refund amount
  - Batch run timestamp

Step 5: VERIFY
─────────────────────────────
□ All affected policies have endorsement created
□ Total refund amount reconciles to expected
□ Finance confirms refund instructions received
□ Spot check 10% of policies manually

Step 6: RECOVER (if error)
─────────────────────────────
□ For incorrectly created endorsements:
  — Raise reversal endorsement to cancel incorrect one
  — Re-run batch for those policies only
□ Do NOT delete endorsements directly from DB
□ Always use application reversal to maintain trail
```

---

---

## Summary Comparison

| | Manual UI | Batch via App | Direct DB | Endorsement Batch |
|---|---|---|---|---|
| **Volume** | < 20 | 20–10,000 | Any (emergency) | 20–10,000 |
| **Risk** | Low | Medium | High | Medium |
| **Audit Trail** | Manual | Auto | Manual | Auto (endorsement) |
| **Rollback** | Reversal endor | Fixed flag + backup | DB restore | Reversal endor |
| **Recommended** | Small fixes | Standard | Last resort | Insurance premium fix |

---

## Non-Negotiable Rules for All Methods

```
✅ Always backup before fix
✅ Always test in UAT first
✅ Always have rollback plan ready
✅ Always write audit log
✅ Always verify after fix
✅ Always get approval before running
✅ Never run fix alone — always have second person
✅ Never skip backup even if "simple" fix
✅ Never modify production DB without transaction control
```

---

*Document Version: 1.0 | Classification: Internal / Confidential*
