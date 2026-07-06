# Task Prerequisite Blocking & Slot Invisibility Defect — QA Reproduction Report

**Task:** `task_fe1252dcc0bc22a2dc3bc216a5298e7a`
**Board:** `task_node_core_product`
**Reporter:** walkonwayvs
**Data source:** Public Task Node task records only (read-only interface)

---

## 1. Summary

A contributor was routed a task their account could not accept, because the task was bound to a stale (old) wallet route. The Task Node interface displayed no dependency or reason on the affected task, so the contributor was left blocked and unaware. This report reproduces the defect entirely from public Task Node records, using two real tasks: the blocked task routed to **jjj**, and the prerequisite unblock task routed to **zoz**.

The blocking window is measurable directly from the public lifecycle timestamps: **~3 days, 7.75 hours** (offered `2026-07-01 01:43 UTC`, accepted `2026-07-04 09:28 UTC`).

## 2. Tasks examined

| Role | Task | Contributor | Task ID |
|---|---|---|---|
| Blocked task | Map Duplicate Reward Payout Pipeline and Recommend Payment Guardrails | jjj (Expert) | `task_5a51195f4c689e2014b7928c4be2a404` |
| Prerequisite / unblock | Unblock jjj's Wallet Route for Duplicate Reward Pipeline Fix | zoz | `task_e0c135ffb2559c3e9a76c4337fb4a2df` |

The stated prerequisite appears **only** in zoz's task "About" text:

> *"jjj (Expert) cannot accept the 25,000 PFT duplicate reward pipeline analysis task because it is bound to an old wallet."*

This text lives on a separate task and is not visible to jjj on the blocked task itself.

## 3. Lifecycle timeline

**Blocked task (jjj) — `task_5a51195f`**

| Event | Timestamp (UTC) |
|---|---|
| Task offered | 2026-07-01 01:43:38 |
| **Task accepted** | **2026-07-04 09:28:35** — ~3d 7.75h after offer |
| Evidence submitted | 2026-07-04 09:46:50 |
| Verification requested | 2026-07-04 09:46:54 |
| Verification response | 2026-07-04 09:47:22 |
| Reward outcome (25,000 PFT) | 2026-07-04 09:47:47 |

**Prerequisite / unblock task (zoz) — `task_e0c135ff`**

| Event | Timestamp (UTC) |
|---|---|
| Task offered | 2026-07-04 05:18:51 |
| Task accepted | 2026-07-04 19:59:15 |
| Reward outcome | 2026-07-04 22:19:47 |

jjj accepted the blocked task at 09:28 on 2026-07-04, roughly 4 hours after zoz's unblock task was offered (05:18) — i.e. acceptance became possible only once the wallet re-route was underway.

## 4. Dependency visibility

**Finding: No.** The blocked task's detail view ("About This Task") contains no dependency field, prerequisite field, blocked-state indicator, or any text explaining why the task could not be accepted (see screenshot). The only record of the prerequisite lives in a *different* contributor's task (zoz's "About"), which the assigned contributor has no path or reason to view. From jjj's side, the task simply sat un-acceptable with no displayed cause.

## 5. Slot-blocking behaviour (single-task constraint)

The defect asserts that the single-task-at-a-time constraint prevents a contributor with a blocked task from accepting alternative work. This half **could not be directly reproduced**: tasks are routed to this operator one at a time, so no state existed in which a blocked-but-accepted task and a second offered task coexisted for live testing. What is verifiable from the public record is the **observable consequence** — jjj could not progress the routed task for ~3d 7.75h until the external wallet re-route completed. Whether the interface hard-blocks alternative acceptance during that window, versus merely leaving the contributor idle, is stated here as untested rather than asserted.

## 6. Root-cause analysis

- **Stale wallet binding at routing time.** The task was bound to a wallet route jjj could no longer use. The routing layer offered it without validating that jjj's current wallet route satisfied the binding.
- **No prerequisite-state check.** Nothing verified the precondition (a valid wallet route) was met before the task was offered, so an un-acceptable task was routed as if actionable.
- **Dependency not surfaced to the contributor.** The blocking reason existed in the system (spelled out in zoz's unblock task) but was never rendered on the affected task, leaving the assigned contributor blocked and uninformed.

## 7. Ranked fix recommendations

1. **Prerequisite-state checking before routing.** Before offering a task bound to a wallet route (or any precondition), validate the target contributor currently satisfies it. If not, hold or re-route rather than offering an un-acceptable task.
2. **Surface dependency / blocked-state info on the task itself.** Render the blocking reason and any prerequisite directly in the assigned contributor's task detail view (e.g. *"Blocked: bound to an inactive wallet route — awaiting re-route"*), so the contributor understands the state without needing access to a separate task.
3. **Auto-release the slot for blocked tasks.** If an accepted task cannot progress due to an unmet prerequisite, do not consume the contributor's single active slot — auto-release it or offer alternative work until the prerequisite clears, so experts are not idled.

## 8. Evidence

Attached screenshots (public, read-only task records): (a) jjj task detail / "About" view showing no dependency field; (b) jjj lifecycle timeline showing the offered→accepted gap; (c) zoz task "About" stating the wallet prerequisite; (d) zoz lifecycle timeline. Plus the Discord announcement link for this task. All data drawn solely from the public Task Node interface.
