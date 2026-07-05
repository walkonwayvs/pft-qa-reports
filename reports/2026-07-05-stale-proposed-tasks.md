# Stale Proposed Tasks Across Active Boards — Cleanup Report

- **Task:** task_bc1648283985337f13832e0ae8e1269e
- **Investigator:** @walkonwayvs
- **Date:** 2026-07-05 (days-stale computed against 2026-07-06)
- **Method:** manual audit of all five active boards via the public Task Node interface (board overview + per-task Forensics)
- **Stale threshold:** >14 days in `proposed` state

## Summary

All five active boards were audited for tasks remaining in `proposed` state for more than
14 days. **Two** confirmed stale proposed tasks were found (both offered 2026-06-21, ~15
days stale), each consuming one routing slot. The June 1 Market Alpha task named in the
assignment (task_8f5e98a1...) **could not be located** on its board through the public
interface and is analysed as a case study. Three of the five boards carry no stale
proposals. The low count is itself a finding: stale-proposal saturation is currently
limited, not widespread.

## 1. Stale-task inventory (age descending)

| Task ID | Title | Board | Proposed | Days | Operator | Reward |
|---|---|---|---|---|---|---|
| task_8f5e98a1833c4d137cb720b94d41ba1f | Design Market-Neutral Alpha Signal Framework (named in task; see case study) | Market Alpha | 2026-06-01 (claimed) | ~35* | not determinable — task not visible | 30,000* |
| task_46dd636c4ea3f747f03cb1293291e4dd | Build Orc Review Pipeline Reconciliation Script | Task Node Core Product | 2026-06-21 | 15 | @hit0ri | 30,000 |
| task_085cf05946dad299d35f00550030604b | Build XRPL Wallet Graph Update Script | Routing Eligibility & Project Governance | 2026-06-21 | 15 | @nydiokar | 24,000 |

\* task_8f5e98a1 values are as claimed in the task description; the task is not visible on
the board, so its date, operator, and reward could not be independently confirmed.

## 2. Per-board summary

| Board | Stale proposals | Routing slots consumed |
|---|---|---|
| Market Alpha | 0 visible (1 referenced but unlocatable) | 0 confirmed |
| Task Node Core Product | 1 | 1 |
| Routing Eligibility & Project Governance | 1 | 1 |
| Community Messaging | 0 | 0 |
| Post Fiat Communications | 0 | 0 |

Total confirmed stale proposals: **2**, consuming 2 routing slots across 5 boards. Three
boards are clean.

## 3. Cleanup recommendations (ranked per item)

- **task_46dd636c (Core Product, 30,000 PFT, 15 days) — TIMEOUT & RE-ROUTE.** Only
  marginally past threshold and describes useful, still-relevant work; re-route from the
  idle assignee (@hit0ri) rather than discard the scope.
- **task_085cf059 (Routing Eligibility, 24,000 PFT, 15 days) — TIMEOUT & RE-ROUTE.** Barely
  stale and a core input to Sybil-risk analysis; re-route from the idle assignee (@nydiokar).
- **task_8f5e98a1 (Market Alpha, named case study) — INVESTIGATE / RECONCILE.** Not visible
  on the board, so no standard cleanup action applies; reconcile the reference first.

## 4. Case study: the unlocatable Market Alpha task

The assignment names task_8f5e98a1833c4d137cb720b94d41ba1f as a June 1 stale proposed task
requiring attention. A full manual review of the Market Alpha board (all task and activity
pages) did **not** surface this task in any state, and there is no task-ID lookup in the
public interface. Three explanations are consistent with the observation:

1. **Already cleaned up / withdrawn** — cancelled or timed-out since the assignment was
   generated; the stale condition has self-resolved.
2. **Never projected to the visible task list** — generated in a Hive/coordination run but
   never rendered onto the operator-facing board.
3. **Stale reference in the task description** — cited from an older coordination summary
   that no longer matches live board state.

Each explanation is itself a task-state reliability issue: either a stale proposal already
existed and was cleaned up, or the assignment references a task operators cannot see — a
stale/phantom reference of the same class documented in the routing-mismatch and
phantom-status reports. Recommended action: reconcile the reference against retained
forensics for that task ID before treating it as actionable.

## 5. Systemic recommendations

- Auto-timeout proposed tasks after a fixed window (e.g. 14 days) with automatic re-route,
  so idle proposals do not hold routing slots indefinitely.
- Surface a proposed-age indicator or status filter in the board UI, so stale proposals are
  visible without opening each task's forensics.
- Reconcile task references in generated assignments against live board state, so tasks like
  task_8f5e98a1 are not cited as actionable when they are not visible on their board.

---

*All findings derive from public, operator-visible Task Node pages. No private or backend
data was accessed. Each confirmed item is checkable by opening the cited task and reading
its status and offer timestamp.*
