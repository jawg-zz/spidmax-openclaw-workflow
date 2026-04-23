# Spidmax OpenClaw Workflow

OpenClaw + OpenCode + Oh My OpenAgent (OmO) coding workflow for Spidmax.

## What This Is

A documented coding workflow where:
- **Max** (the AI assistant) handles non-coding tasks
- **OpenCode + OmO** handles all app coding — full cycle, start to finish
- You just give Max the task

```
You → Max (task) → OpenCode+OmO (full cycle) → Max (report back)
```

## Quick Start

1. **Clone this repo** to your OpenClaw workspace
2. **Install OpenCode + OmO:**
   ```bash
   npm install -g opencode-ai
   opencode plugin install oh-my-openagent
   ```
3. **Set API key** in `/etc/environment`:
   ```
   OPENCODE_API_KEY="your-key-here"
   ```
4. **Done** — Max now handles coding tasks via OpenCode+OmO

## The Workflow

1. You give Max the task
2. Max spawns OpenCode+OmO via ACP
3. OmO handles everything: classify → inspect → research → plan → implement → verify → review
4. Max reports back to you

## Key Files

| File | Purpose |
|------|---------|
| `spidmax_openclaw_coding.md` | Full onboarding & setup guide |
| `MEMORY.md` | Canonical coding rule (single source of truth) |
| `SOUL.md` | Max's role definition |
| `AGENTS.md` | Agent delegation rules & spawn syntax |
| `TOOLS.md` | Spawn templates & configs |

## OmO Commands

- `ulw` or `ultrawork` — activates full OmO discipline system
- Max always uses these for coding tasks

## Official Docs

- [Oh My OpenAgent](https://github.com/code-yeongyu/oh-my-openagent)
- [OpenCode](https://opencode.ai/docs/)
- [OpenClaw](https://docs.openclaw.ai)

## Need Help?

1. Read `spidmax_openclaw_coding.md` for full setup
2. Check `MEMORY.md` for the canonical coding rule
3. Review `TOOLS.md` for spawn templates

---

*Part of the Spidmax OpenClaw setup*