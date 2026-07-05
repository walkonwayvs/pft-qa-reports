# Task Routing Reference Mismatch Bug — QA Findings

- **Task:** task_468273076a0e055a36dc58da7e8ce264
- **Investigator:** @walkonwayvs
- **Date:** 2026-07-05
- **Method:** public Hive board packet, Hive Context, and read-only task records; no backend access

## Summary

The Task Node coordination layer referenced **task_78ab6c03** to **@jollydinger** as work to
be done. That task ID actually belongs to **@jjj**, is titled "Analyze Rejected Task Capacity
Release Bug," and had already been **completed and rewarded (20,000 PFT) on 2026-07-01** —
three days before the coordination churn that surfaced the mismatch. The system pointed one
operator at a task that was both assigned to a different operator and already closed. This is
a reference-integrity failure in the coordination/task-generation layer.

## 1. Timeline

| When (UTC) | Event |
|---|---|
| 2026-06-29 23:27 | task_78ab6c03 "Analyze Rejected Task Capacity Release Bug" offered to **@jjj** (20,000 PFT). |
| 2026-07-01 11:21 | task_78ab6c03 **rewarded to @jjj** — complete and closed. |
| 2026-07-04 02:23 | "Coordinate Wallet Re-route to Unblock jjj's Task Acceptance" (task_2c1fca9f) offered to **@jollydinger** (10,000 PFT). |
| 2026-07-04 05:18 | "Unblock jjj's Wallet Route for Duplicate Reward Pipeline Fix" (task_e0c135ff) offered to **@zoz**. |
| 2026-07-04 16:51 | @jollydinger **refuses** task_2c1fca9f. |
| 2026-07-04 22:19 | @zoz's task_e0c135ff closed **rewarded but 0 PFT** (required coordination artifacts not demonstrated). |
| board packet (4 Jul) | Project Status states: the coordination system told **jollydinger** to work on **task_78ab6c03**, but that task was assigned to **jjj**. |

## 2. Cross-reference: communicated vs actual

| Attribute | As communicated (to @jollydinger) | Actual record |
|---|---|---|
| Task ID | task_78ab6c03 | task_78ab6c03d0e1803f22d65f4eb2e536ce |
| Assigned operator | @jollydinger (implied by routing message) | **@jjj** (rUjqjr...rPFdn) |
| Title | (unspecified in the reference) | Analyze Rejected Task Capacity Release Bug |
| Status | presented as work still to be done | **Rewarded / closed** (20,000 PFT) |
| Completed | — | 2026-07-01, i.e. 3 days before the reference |

## 3. Root-cause analysis

The mismatch is a **reference-integrity failure in the coordination / task-generation
layer**, not operator error. Three properties of the referenced task were wrong at once — its
owner (jjj, not jollydinger), its state (closed, not open), and its currency (completed three
days earlier). Supporting evidence:

- The wider coordination cluster (unblocking jjj's wallet) was churny: @zoz's task closed
  "rewarded but 0 PFT" and @jollydinger's parallel task was refused.
- @goodalexander reported on 2026-06-19 that the Hive "is not capable of outputting messages"
  reliably to operators following up on their tasks.
- The Hive Secretary report lists "hallucinated references" and "capacity/routing confusion"
  as active reliability signals.
- There is no operator-facing way to resolve a task ID to its owner/status, so a stale or
  fabricated reference passes downstream unchecked.

**Most likely cause:** the coordination agent composed a follow-up from stale context and
attached a task ID without validating, at send time, that the ID was open and owned by the
recipient.

## 4. Fix recommendations

- **Fix 1 — Reference pre-check at send time.** Validate any cited task ID against the live
  store before emitting: it must exist, be open, and be owned by the recipient.
- **Fix 2 — Owner + status binding in the reference.** Carry structured references
  (task_id + owner + status + last_event), so a mismatch is machine-detectable.
- **Fix 3 — Operator-facing task-ID resolver.** Expose a read-only lookup from task ID to
  owner, title, status, and timestamps.

---

*All findings derive from public, operator-visible records. Task IDs and outcomes are quoted
as shown in the interface.*
