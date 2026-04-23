# Spidmax OpenClaw Coding Workflow

## Overview

This document describes the coding workflow for the Spidmax OpenClaw instance. It covers setup, the canonical workflow, and how to maintain it.

## Official References

- **OmO Docs:** https://github.com/code-yeongyu/oh-my-openagent
- **OpenCode Docs:** https://opencode.ai/docs/
- **OpenClaw Docs:** https://docs.openclaw.ai

---

## The Workflow

```
You → Max (task) → OpenCode+OmO (full cycle) → Max (report back)
```

**Steps:**
1. You give Max the task
2. Max spawns OpenCode+OmO via ACP
3. OmO handles everything: classify → inspect → research → plan → implement → verify → review
4. Max reports back to you

---

## Canonical Rule

The single source of truth is **MEMORY.md** in the workspace. It contains:

```markdown
## Coding Rule (CANONICAL)

1. You give me the task
2. I spawn OpenCode+OmO with the task
3. OmO handles everything: classify, inspect, research, plan, implement, verify, review
4. I report back to you
```

---

## Setup

### 1. OpenCode + OmO Installation

```bash
# Install OpenCode
npm install -g opencode-ai

# Install OmO plugin
opencode plugin install oh-my-openagent
```

### 2. Configure OpenCode

Edit `~/.config/opencode/opencode.json`:

```json
{
  "plugins": ["oh-my-openagent"],
  "rules": ["~/.config/opencode/rules.karpathy.md"],
  "agents": {
    "sisyphus": "opencode-go/minimax-m2.7",
    "hephaestus": "opencode-go/minimax-m2.7"
  },
  "categories": {
    "visual-engineering": "opencode-go/minimax-m2.7",
    "ultrabrain": "opencode-go/minimax-m2.7",
    "deep": "opencode-go/minimax-m2.7",
    "artistry": "opencode-go/minimax-m2.7",
    "quick": "opencode-go/minimax-m2.7",
    "unspecified-low": "opencode-go/minimax-m2.7",
    "unspecified-high": "opencode-go/minimax-m2.7",
    "writing": "opencode-go/minimax-m2.7"
  }
}
```

### 3. Set API Key

```bash
# Add to /etc/environment or ~/.bashrc
export OPENCODE_API_KEY="your-key-here"
```

---

## How Max Spawns OpenCode

Max uses `sessions_spawn` with the ACP runtime:

```typescript
sessions_spawn({
  task: `Your task description here.

## OmO Instruction (REQUIRED)
Run \`ulw\` / \`ultrawork\` to activate OmO's full discipline system.

## Karpathy Discipline
- State assumptions explicitly
- Minimum code that solves the problem
- Touch only what you must
- Define success criteria and verify

## Verification
- [build commands]
- [test commands]`,
  runtime: "acp",
  agentId: "opencode",
  mode: "run",
  streamTo: "parent",
  runTimeoutSeconds: 300
})
```

---

## Document Hierarchy

| File | Purpose |
|------|---------|
| **MEMORY.md** | Canonical coding rule (single source of truth) |
| **spidmax_openclaw_coding.md** | This file — onboarding & setup reference |
| **TOOLS.md** | Spawn templates & technical details |
| **AGENTS.md** | Agent behavior & spawn syntax |
| **SOUL.md** | Max's role definition |

---

## Updating the Workflow

If you change the workflow:

1. **Update MEMORY.md** — it's the canonical source
2. **This file** — update setup/onboarding if needed
3. Other docs will reference MEMORY.md automatically

---

## Karpathy Discipline

Always included in every task packet:

1. **Think before coding** — state assumptions, surface ambiguity
2. **Simplicity first** — minimum code, no speculative features
3. **Surgical changes** — touch only what you must
4. **Goal-driven** — define success criteria, verify

See: `~/workspace/skills/karpathy-discipline/SKILL.md`

---

## Troubleshooting

- **ACP silence is normal** — OpenCode works silently, no real-time output
- **Don't check subagents/process list** — ACP agents don't show there
- **Stream log:** Check `/data/.openclaw/agents/opencode/sessions/<id>.acp-stream.jsonl`

---

*Last updated: 2026-04-23*