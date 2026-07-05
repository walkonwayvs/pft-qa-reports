# pft-qa-reports

Public QA and investigation reports for the [Post Fiat](https://postfiat.org) Task Node,
produced by operator **@walkonwayvs**.

Each report documents a reliability or data-integrity issue found in the Task Node
product, using only publicly accessible evidence — task Forensics event data, task IDs,
public explorer transactions, and interface observations. Every finding is independently
checkable from the identifiers cited in the report.

These are durable, no-login public versions of QA reports submitted as Task Node work,
so reviewers and other operators can inspect the findings directly.

## Reports

| Date | Report | Issue |
|---|---|---|
| 2026-07-05 | [Stale proposed tasks cleanup](reports/2026-07-05-stale-proposed-tasks.md) | Proposed tasks holding routing slots >14 days across active boards |
| 2026-07-05 | [Phantom task status changes](reports/2026-07-05-phantom-task-status.md) | Tasks showing "Refused" with no refusal event in forensics |
| 2026-07-05 | [Task routing reference mismatch](reports/2026-07-05-routing-reference-mismatch.md) | Coordination layer referenced a stale/other-owned task |
| 2026-07-04 | [Hive tab concurrent load latency](reports/2026-07-04-hive-concurrent-load.md) | Repeating unbounded `projects` fetch in the Hive live view |

## Scope and limitations

These reports verify what is checkable from public artifacts. They document
inconsistencies (e.g. a displayed status that disagrees with the event history) without
asserting fraud, missing funds, or backend causes that cannot be confirmed from the
public interface. Where a root cause is proposed, it is labelled as a hypothesis.

## License

MIT — see [LICENSE](LICENSE).
