# Sources

Every factual claim in this course traces to a row below. If it is not here, it is not in a
lesson.

**Tiers**, highest authority first: `standard` (specs, RFCs, laws) · `official docs`
(vendor or project documentation) · `internal policy` (this organization's own documents) ·
`vendor blog` · `community` (tutorials, forum posts — acceptable for showing how something
is commonly done, never as the authority for what is true).

`checked-on` is the date the source was last read and confirmed. A refresh run re-checks
anything older than the brief's stale-by date.

| ID | Title | Location | Type | Tier | Checked on | Cited in | Notes |
| --- | --- | --- | --- | --- | --- | --- | --- |
| `kafka-retention` | Kafka broker configs | https://kafka.apache.org/documentation/#brokerconfigs | official docs | 2 | 2026-09-02 | 03 | |
| `escalation-policy` | Support escalation policy §4 | `docs/support/escalation.md#tier-2` | internal policy | 3 | 2026-09-02 | 05, 06 | Conflicts with `runbook-oncall`; author confirmed this one is authoritative. |

## Conflicts

<Record every case where sources disagreed, how it was resolved, and by whom. When both
conflicting sources are internal, this section is also a bug report for the author's
documentation.>

- **<topic>:** `<source-a>` says <X>, `<source-b>` says <Y>. Resolved to <X> on <basis:
  higher tier | author decision on YYYY-MM-DD | taught as a callout in lesson NN>.

## Gaps

<What the outcome required that no available source covered. Each one either became a web
citation, a `> **Gap:**` callout in a lesson, or a blocker the author resolved.>

- **<topic>:** not covered by <corpus>. <Resolution.>
