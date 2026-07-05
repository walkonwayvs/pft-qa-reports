# Hive Tab Concurrent Load Latency — QA Reproduction

- **Task:** task_4e102f3c3c35fbc63da2449558a9e41b
- **Investigator:** @walkonwayvs
- **Date:** 2026-07-04
- **Environment:** Brave 1.89.143 (Chromium 147.0.7727.117), Pop!_OS Linux
- **Method:** DevTools Network panel with "Disable cache" and "Preserve log" enabled; no backend access

## Summary

The reported multi-second concurrent-load latency did **not** reproduce under any tested
condition; individual requests stayed fast (100–450ms, all HTTP 200) across normal,
concurrent (two tabs), and rapid-refresh access. One reproducible anomaly was found: in a
clean logged-out session, the Hive live view issues a **`projects` fetch that repeats
continuously and never settles** — request count climbed from ~15 to 67+ over ~8 minutes
with no user interaction, ~110kB re-fetched each cycle, timeline past 500,000ms, ~5.1MB
transferred and growing.

## Conditions tested

| Condition | Result |
|---|---|
| Single tab (logged in) | balance?force=1 requests 122–134ms, HTTP 200; UI painted immediately |
| Two tabs concurrent | 124–156ms, HTTP 200; no pending backlog, no timeouts |
| Rapid refresh | ~124ms, HTTP 200; page finish driven by ongoing polling, not any single slow request |
| Clean private session, 2 tabs, left open | `projects` fetch repeats endlessly; request count 15 → 67+ over ~8 min |

## Notes

- Console errors observed were from browser wallet extensions (inpage.js, Backpack,
  polkadot) and Permissions-Policy warnings — **not** the Task Node app. Excluded as noise;
  re-tested in a clean private window to confirm.
- The `balance?force=1` requests are login-gated and do not fire when logged out.

## Reproducibility

The specific latency was not reproduced; requests stayed fast under every condition. The
reproducible anomaly is an unbounded, continuously-repeating `projects` fetch in the Hive
live view — a Hive tab left open accumulates requests and bandwidth without bound (~1 request
/ 7s, ~110kB each, never settling). May be intended live-refresh, but the full re-fetch each
cycle with no settling is a plausible contributor to perceived heaviness under concurrent
Hive tabs. Severity: low-to-moderate (no hard failure or timeout; resource growth over time).

---

*All findings derive from public, operator-visible behaviour captured in the browser
DevTools Network panel. No private or backend data was accessed.*
