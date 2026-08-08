# Rich Vibecoder planning verification rules

Use this checklist after every planning run.

## Before execution

- Confirm there is one normalized task.
- Confirm the shared context packet contains constraints, acceptance criteria, and planning depth.
- Confirm five planner requests exist with unique IDs and exactly one of each planner variant.
- Confirm every request has identical task/context digests and the same read-only rule.
- Confirm the host can create and collect five planner responses, or report the missing capability before claiming execution.

## During execution

- Prefer parallel dispatch; sequential dispatch is valid only as a host limitation fallback.
- Permit read-only inspection only.
- Reject or stop any planner that attempts to edit files, create artifacts, mutate dependencies, commit, branch, create a worktree, apply a patch, or use an external side effect.
- Do not share planner outputs before all five planners finish or reach a terminal state.
- Record failures, timeouts, cancellations, and collection errors rather than retrying as an untracked sixth planner.

## Before selection

- Confirm all five planning records are present.
- Confirm each eligible plan is actionable and covers mandatory acceptance criteria.
- Apply every hard gate in `selection-contract.md`.
- Record score breakdowns, passed gates, failed gates, and rejection reasons for every planner.
- Confirm the ranking is deterministic and exactly one plan is selected when at least one plan is eligible.

## Handoff checks

- Confirm the selected output is a plan, not an implementation artifact.
- Confirm the handoff includes assumptions, risks, expected files/components, execution order, and later validation steps.
- Confirm `changes_made: false` in the report.
- Confirm no code, project files, dependencies, branches, worktrees, commits, or external systems were changed.
- Do not report promotion, cherry-pick, implementation, or code verification as part of this skill.

## Required scenarios

A harness adapter should exercise at least these cases:

1. Five successful planners with a deterministic winner.
2. One failed or timed-out planner with four plans still evaluated.
3. A partial planner response that is and is not actionable enough to compete.
4. No planner passing the hard gates, resulting in no implementation handoff.
5. A host without parallel execution, using the same five requests sequentially.
6. A host without sub-agent execution, returning prepared planner requests without claiming runs.
7. A planner attempting a write or other mutation, which must be rejected or recorded as a scope violation.
