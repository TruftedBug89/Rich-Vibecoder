# 🧠 Rich Vibecoder

**Portable Agent Skill for planning major, complex, and long-horizon work.**

Rich Vibecoder runs **five independent, read-only planning agents** on the same task and context, compares their plans, and produces one implementation handoff for a later coding phase.

> [!NOTE]
> **Project Status:** 🟢 **Working / Functional**  
> *This status must not be changed by AI unless explicitly instructed by the repository owner.*

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](LICENSE)
[![Agent Skills](https://img.shields.io/badge/Agent%20Skills-SKILL.md-purple)](skills/rich-vibecoder/SKILL.md)
[![OpenSkills](https://img.shields.io/badge/OpenSkills-ready-orange)](https://github.com/numman-ali/openskills)

## Why five planners?

Single-agent plans have blind spots. Five planners with distinct mandates surface trade-offs, risks, and design options that one pass would miss. A selection contract then merges their strengths into a single, deterministic winner.

## The five planners

| Planner | Mandate |
|---|---|
| **Baseline** | Clearest minimal plan that follows the repository's existing conventions |
| **Robustness & Risk** | Failure modes, edge cases, compatibility, recovery, security |
| **Architecture** | Clean integration, maintainable boundaries, data flow |
| **Verification** | Testability and reproducible evidence |
| **Alternative** | A genuinely different design, when justified |

## Safety model

Planners are **read-only by contract**:

- They inspect the repository or artifact only through read-only capabilities.
- No edits, creates, deletes, worktrees, branches, commits, patches, or installs.
- The selected output is a **plan**, never an implementation artifact.
- Hard gates + weighted scoring + deterministic tie-breakers are defined in [`selection-contract.md`](skills/rich-vibecoder/references/selection-contract.md).

## Skill package

```text
skills/rich-vibecoder/
├── SKILL.md
└── references/
    ├── prompt-templates.md
    ├── selection-contract.md
    └── verification.md
```

## Install

### OpenSkills

```text
npx openskills install TruftedBug89/Rich-Vibecoder --universal
npx openskills sync
```

### Manual / local development

Copy or link `skills/rich-vibecoder/` into the target harness's skill directory. The canonical skill name is `rich-vibecoder`; `/rich` may be configured as a host-specific alias. CommandCode can consume the same package through its own optional skill-directory integration, but it is not required.

## Distribution

The package follows the portable `SKILL.md` format and can be adapted by harnesses that support Agent Skills. OpenSkills provides a cross-agent installer and `AGENTS.md` discovery bridge. skills.sh and other registries may index public Git repositories according to their own submission rules.

> Review the skill instructions and bundled references before installing them into an agent environment.

## References

- [Agent Skills specification](https://agentskills.io/specification)
- [OpenSkills](https://github.com/numman-ali/openskills)
- [skills.sh](https://www.skills.sh/)

## License

[MIT](LICENSE) © 2026 TruftedBug89
