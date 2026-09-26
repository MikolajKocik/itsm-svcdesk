---
lab2_edge_cases:
  E1: {rule: R-08, count: 3}
  E2: {rule: R-06, count: 2}
  E3: {rule: R-09, count: 4}
  E4: {rule: R-10, count: 4}
  E5: {rule: R-12, count: 1}
  E6: {rule: R-13, count: 11}
---
<!-- ai-generated: 100% - Antigravity generated the documentation -->

# Edge cases in the practice event log

## E1 - clock skew produces a negative lead time

- What the log contains: A commit timestamped after the deployment that shipped it.
- What a default definition would have done: It would have computed a negative lead time and skewed the median downward, or crashed.
- Why the rule is defensible: Clamping to zero reflects that the change was delivered immediately upon (or before) its official creation, while preventing negative values from distorting the median.

## E2 - a revert of a revert

- What the log contains: A commit that reverts another commit which itself is a revert.
- What a default definition would have done: It would treat it as a brand new change or fail to link it to the original intent.
- Why the rule is defensible: A revert of a revert restores the original change, so transitively assigning it the original `change_id` correctly tracks the lifecycle of the actual feature.

## E3 - a hotfix that never touched `main`

- What the log contains: A commit deployed to production directly from a side branch.
- What a default definition would have done: It might ignore it if it filters by `branch == "main"`.
- Why the rule is defensible: Production delivery is what matters for DORA metrics; the branching strategy or branch name is an implementation detail.

## E4 - a deployment with zero linked commits

- What the log contains: A production deployment with an empty `commits` array.
- What a default definition would have done: It might crash or incorrectly count it as having delivered something, or skip it entirely in frequency counts.
- Why the rule is defensible: A deployment happened (e.g. a config change or rollback not tracked via commits) and should count towards deployment frequency and fail rate, even if no code changes were delivered.

## E5 - a deployment that failed and never recovered

- What the log contains: A failed deployment with a covering incident that has no `resolved` event.
- What a default definition would have done: It might invent a recovery time (e.g. to the end of the window) or crash.
- Why the rule is defensible: An unresolved failure has no recovery time yet, so excluding it from the median while counting it as an open failure prevents artificial skewing of the metric.

## E6 - overlapping incidents

- What the log contains: Multiple incidents whose time intervals overlap.
- What a default definition would have done: It might merge their durations or double-count the downtime.
- Why the rule is defensible: Recovery time is about the specific failed deployment, not overall system downtime. Each deployment's recovery is measured by its specific covering incident.

## Gaming demonstration

We improved `deployment_frequency_per_day` by exploiting rule R-10 (deployments without commits count). In a real team, the incentive to do this would be a management target to "deploy more often". The team would be rewarded for pushing "empty" deployments to production just to inflate the metric, while actual feature delivery (ground truth) suffered because real work was delayed (moved to the future).
