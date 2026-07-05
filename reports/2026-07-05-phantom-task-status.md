# Phantom Task Status Changes in Task Node — QA Findings

- **Task:** task_c66958996a31a84e8b94d2d03b9fc8a9
- **Investigator:** @walkonwayvs
- **Date:** 2026-07-05
- **Method:** public Task Node task pages (Overview + Forensics) and Hive reports; no backend access

## Summary

Multiple Network tasks display a terminal status of **Refused** despite the operator never
seeing or interacting with them. On these tasks the Forensics event history contains a
**single** indexed event — the initial offer — and the Projection reducer shows only
**Task offered**, with **no refuse event**. By contrast, a genuinely refused task shows
**two** events (offer + refusal) and a reducer containing both **Task offered** and
**Task refused**. The displayed status therefore contradicts the recorded event history:
the UI labels system-initiated refusals/cancellations as user **Refused**. Confirmed across
two operators.

## 1. Phantom status instances

| Operator | Task ID | Title | Displayed | Expected | Evidence (indexed events) |
|---|---|---|---|---|---|
| walkonwayvs | task_95a6572625aeecc6d90a43e01cb44eb1 | Build Sybil Contagion Path Propagation Analyzer | Refused | Offered / system-cancelled | 1 event: Task offered only; reducer = Task offered |
| walkonwayvs | task_3afd1ffbef681e764c3e712f71c6559a | Build Sybil Blocklist Query API Server | Refused | Offered / system-cancelled | 1 event: Task offered only; Lifecycle = Offered |
| walkonwayvs | task_c1b09eec1de2f358d797f55b283b5822 | Build Orc Review Ledger Export Script | Refused | Offered / system-cancelled | 1 event: Task offered only; Lifecycle = Offered |
| georgl0nggamma | task_f4d1e95862e0c6a3f5c894cd23867855 | Draft Discord Ban And Clawback Action Packet | Refused | Offered / system-cancelled | 1 event: Task offered (Jun 16) only; no refuse event |

### Control group — genuine user refusals (contrast)

| Operator | Task ID | Displayed | Evidence |
|---|---|---|---|
| walkonwayvs | task_d932d4aba54952da2feda7798f0f22a3 | Refused | 2 events: Offered + Refused; reducer = Task offered + Task refused |
| georgl0nggamma | task_5a20810ab95649a6019c048014a91f5a | Refused | 2 events: Task offered + Task refused |

## 2. Reproduction (public interface only)

1. Open any task in the **Refused** section of the Tasks overview.
2. Open its **Forensics** tab; read the **Lifecycle** stages and the **Projection reducer**.
3. **Phantom:** Lifecycle shows only **Offered**, the timeline has a single `pf.task.offer.v1`
   event (transition *proposed*), and the reducer contains only **Task offered** — yet the
   Overview status reads **Refused**.
4. **Genuine refusal (control):** Lifecycle shows **Offered → Refused**, two events, reducer
   contains both **Task offered** and **Task refused**.
5. The reducer entry count is the reliable discriminator: **one** entry = phantom; **two** =
   real refusal.

## 3. Root-cause hypotheses (ranked)

1. **System-initiated refusal/cancellation is not written as a distinct event (most likely).**
   When the network/Board Manager refuses or cancels an unaccepted offer, it updates the
   displayed status to Refused but appends no corresponding event. Matches @goodalexander's
   note that "the network refused them for you or cancelled them."
2. **Missing status vocabulary** — no Cancelled/Expired/Auto-refused state, so the UI reuses
   the user-facing **Refused** label for a different underlying cause.
3. **Overview status not reconciled against the reducer** — the displayed status is never
   validated against the authoritative event history.

## 4. Recommended fixes

- **Fix 1:** emit a distinct event for every terminal transition (e.g. `pf.task.cancel` /
  `pf.task.auto_refuse` / `pf.task.expire`), so status is always backed by an event.
- **Fix 2:** add explicit statuses (Cancelled, Expired, Auto-refused) and show them in the UI
  instead of labelling system actions as user **Refused**.
- **Fix 3:** reconcile the overview status against the reducer; if a task shows Refused with
  no refuse/cancel event, flag it as inconsistent.

---

*All findings derive from public, operator-visible Task Node pages. Every instance is
checkable by opening the cited task's Forensics tab and comparing the displayed status
against the Projection reducer event count.*
