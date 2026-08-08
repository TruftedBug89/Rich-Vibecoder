# Rich Vibecoder planning selection contract

Normalize every planner response into one planning record. Preserve the original response separately when the host supports it.

```yaml
planner_id: string
agent_index: 1-5
run_id: string
status: succeeded | partial | failed | cancelled | timed_out
plan_summary: string
current_state: string
proposed_steps: []
proposed_files: []
tradeoffs: []
risks: []
assumptions: []
verification_steps: []
provenance:
  task_digest: string
  shared_context_digest: string
  planner_variant: baseline | robustness | architecture | verification | alternative
  adapter_run_id: string
  model: string | null
  started_at: string | null
  completed_at: string | null
```

## Hard gates

Evaluate these gates before numeric scoring:

1. Exactly five planner requests were created and exactly five result records were recorded.
2. The plan addresses the normalized task rather than a different interpretation.
3. The plan is actionable enough for a later implementation phase.
4. Mandatory acceptance criteria are accounted for.
5. Risks, assumptions, and later verification steps are identified where relevant.
6. The planner remained read-only and claims no repository or external changes.
7. Task/context digests, planner identity, variant, and run identity are present.

A failed, cancelled, or timed-out planner cannot pass the gates. A partial plan may pass only if it remains complete enough to guide implementation and evaluate safely.

## Weighted score

Score only plans that pass all hard gates. Use a 0-100 scale:

- Requirement coverage and technical correctness: 30 points
- Implementation clarity and actionable sequencing: 25 points
- Architecture and integration quality: 20 points
- Risk, edge-case, and compatibility analysis: 15 points
- Verification strategy and evidence quality: 10 points

The task's explicit acceptance criteria take priority over this generic rubric. Deduct points for unsupported assumptions, unnecessary complexity, unrelated scope, missing risks, or weak verification steps.

## Deterministic tie-breakers

When plans have the same total score, choose in this order:

1. More complete coverage of mandatory acceptance criteria.
2. More actionable file/component boundaries and implementation sequence.
3. Stronger risk and compatibility analysis.
4. More reproducible verification steps.
5. Lowest `agent_index`.

Record every plan's score breakdown and rejection reasons. Never report a winner without a rationale.

## Selection and handoff semantics

The winner is a plan, not code. Select exactly one plan by default and pass it to a later implementation phase as an implementation handoff.

The handoff should include:

- the selected plan in full;
- why it won;
- assumptions that must be confirmed during implementation;
- risks and unresolved questions;
- files or components expected to change;
- validation steps to run after implementation;
- an explicit statement that the planning phase made no changes.

Do not merge, synthesize, apply, commit, cherry-pick, or promote planner outputs. If a future workflow wants to combine useful ideas from multiple plans, make that a separate explicit synthesis phase.
