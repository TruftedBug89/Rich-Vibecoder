---
name: rich-vibecoder
description: Plan one major, complex, or long-horizon task through exactly five independent read-only planning agents, compare their plans, and select the strongest implementation plan. Use when a task benefits from parallel architecture, risk, testing, or alternative-design analysis before coding.
---

# Rich Vibecoder

Use this skill as a planning-only orchestration protocol, not as a provider-specific API. The canonical skill name is `rich-vibecoder`; a host may expose it as `/rich` when custom aliases are supported.

## Scope

This skill summons exactly five planners. They inspect the current repository or artifact and produce independent implementation plans. They do not implement, edit, create, delete, move, apply, commit, cherry-pick, or otherwise mutate code or project files.

The actual implementation is a separate phase. After selecting a winning plan, hand that plan to the main agent or an explicitly authorized implementation workflow. Do not begin implementation as part of this skill.

Do not create isolated workspaces, worktrees, branches, artifact namespaces, or temporary copies for the planners. All planners use the same read-only task context and target workspace. Their outputs are isolated as responses, not as code workspaces.

## Inputs

Require one normalized planning task and one shared context packet. The packet must include:

- `task`: one actionable objective, not unrelated tasks combined together.
- `context`: repository or artifact facts, relevant files, interfaces, and current state.
- `constraints`: safety, compatibility, scope, and tooling limits.
- `acceptance_criteria`: observable conditions the eventual implementation must satisfy.
- `planning_depth`: the expected level of architectural and implementation detail.

If the task is ambiguous or combines unrelated objectives, ask for clarification instead of inventing priorities.

If a host cannot create sub-agents, do not claim that five planners ran. Return the five prepared planning requests or report the missing capability.

## Workflow

1. Normalize the task and shared context. Freeze the common packet before creating planner requests.
2. Create exactly five read-only planning requests using `references/prompt-templates.md`. Vary only the planning role or strategy emphasis.
3. Give every planner the identical task, facts, constraints, acceptance criteria, and planning depth.
4. Run all five in parallel when the host supports it. If the host cannot parallelize, run the same five requests sequentially without changing their inputs.
5. Permit repository inspection only through read-only capabilities. Do not permit file writes, code edits, shell commands that mutate state, commits, branches, worktrees, package installs, or external side effects.
6. Keep planners independent during analysis. Do not show one planner's plan, score, or failure to another planner before all five responses are collected.
7. Collect exactly five planning result records before ranking. Record successful, partial, failed, cancelled, and timed-out runs; never replace a failed run with a sixth planner.
8. Apply the hard gates in `references/selection-contract.md` before assigning scores. A detailed plan that violates read-only scope is ineligible.
9. Score eligible plans with the documented rubric and deterministic tie-breakers. Select one winning plan; do not silently merge plans.
10. Produce an implementation handoff containing the selected plan, its rationale, rejected-plan summaries, assumptions, risks, and proposed verification steps. Do not apply the plan.
11. Return the structured planning report described below.

## Required planning report

Return a machine-readable report when the host supports structured output. Include at least:

```yaml
run_id: string
agent_count: 5
task_digest: string
shared_context_digest: string
runs:
  - planner_id: string
    agent_index: 1
    status: succeeded | partial | failed | cancelled | timed_out
    hard_gates: pass | fail
    score: number | null
    rejection_reasons: []
    plan_summary: string
selection:
  winner_id: string | null
  rationale: string
implementation_handoff:
  status: ready | not_ready
  plan: string | null
  assumptions: []
  risks: []
  verification_steps: []
changes_made: false
provenance: {}
```

Always state that no code or project files were changed. When structured output is unavailable, preserve these fields in a readable report.

## Failure rules

- Continue collecting the other four plans when one planner fails, times out, or is cancelled.
- A partial plan may compete only when it is complete enough to evaluate and contains actionable planning detail.
- If no plan passes the hard gates, return no implementation handoff and report remediation details.
- If collection fails, preserve the planner identity and collection error.
- Never implement, edit, or promote a plan from inside this skill.
- Never claim that a plan was applied, committed, cherry-picked, or verified as code.

Read the supporting references before constructing planner prompts or selecting a plan:

- [Prompt templates](references/prompt-templates.md)
- [Selection contract](references/selection-contract.md)
- [Verification rules](references/verification.md)
