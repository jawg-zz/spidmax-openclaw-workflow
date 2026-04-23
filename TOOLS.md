# TOOLS.md - Local Notes

Skills define _how_ tools work. This file is for _your_ specifics — the stuff that's unique to your setup.

## Startup Optimizations

- Profile script: `/etc/profile.d/openclaw-optimizations.sh`
- `NODE_COMPILE_CACHE=/var/tmp/openclaw-compile-cache` — faster CLI startup
- `OPENCLAW_NO_RESPAWN=1` — avoids extra startup overhead
- Need container/instance restart to apply to PID 1

## Context Management

**Always truncate tool outputs** — never dump raw API responses or long logs.
- Use `tail -15` or `head -20` on exec output by default
- Only show full output if specifically debugging
- Summarize results instead of echoing them

**Compact proactively** — run `/compact` after completing a major phase:
- After memory cleanup
- After finishing a deployment/test cycle
- Before starting a new investigation
- When context exceeds ~70%

**Kill background processes fast** — don't let polls run for minutes:
- Set reasonable timeouts (30-60s max)
- Check result once, then stop
- Don't show every poll iteration — just the final result

**Screenshots** — avoid dumping unless needed for debugging

## What Goes Here

Things like:

- Camera names and locations
- SSH hosts and aliases
- Preferred voices for TTS
- Speaker/room names
- Device nicknames
- Anything environment-specific

## Examples

```markdown
### Cameras

- living-room → Main area, 180° wide angle
- front-door → Entrance, motion-triggered

### SSH

- home-server → 192.168.1.100, user: admin

### TTS

- Preferred voice: "Nova" (warm, slightly British)
- Default speaker: Kitchen HomePod
```

## Why Separate?

Skills are shared. Your setup is yours. Keeping them apart means you can update skills without losing your notes, and share skills without leaking your infrastructure.

---

Add whatever helps you do your job. This is your cheat sheet.

## Coding

### OpenCode (Primary)
- **Binary:** `opencode` (v1.3.17, npm: `opencode-ai`)
- **Config:** `/data/.config/opencode/opencode.json`
- **API Key:** `OPENCODE_API_KEY` env var (set in `/etc/environment`)
- **Default Model:** `opencode-go/minimax-m2.7` (via opencode-go provider)
- **Usage:** `opencode run "task"` or spawn via ACP (`sessions_spawn`)

### Oh My OpenAgent (OmO)
- **Plugin:** `oh-my-openagent@latest` in opencode config
- **Config:** `/data/.config/opencode/oh-my-openagent.json`
- **All agents mapped to:** `opencode-go/minimax-m2.7`
- **All categories mapped to:** `opencode-go/minimax-m2.7`
- **Key command:** `ulw` / `ultrawork` activates full discipline agent system

### Workflow

**See MEMORY.md for the canonical coding rule.**

This section has the spawn templates and technical details.

### Standard OpenCode Spawn Template

Use this as the default task packet whenever I spawn OpenCode for coding work:

```markdown
[{{timestamp}}] You are working on `{{project_path}}`.

## Task Classification
- Type: {{review|debug|plan|implement|deploy}}
- Execution mode: {{analysis-only|plan-first|implement-now}}
- OmO mode: {{use native OmO if helpful|native OmO required|do not use OmO for this task}}

## First Steps
1. Inspect the real repo/files involved before making changes.
2. State assumptions and unknowns explicitly.
3. If the task is ambiguous, surface the ambiguity before coding.

## Goal
{{what needs to be true when this task is done}}

## Scope
- Touch only: {{files_or_areas}}
- Do not change: {{out_of_scope_items}}

## Relevant Context
- Files/modules: {{relevant_files}}
- Current behavior/problem: {{observed_issue_or_context}}
- Constraints: {{technical_or_product_constraints}}

## OmO Instruction (REQUIRED)
- OpenCode is the coding runtime.
- OmO is natively installed inside OpenCode.
- **ALWAYS run `ulw` / `ultrawork` after initial repo inspection.**
- Default OmO shape: repo-first exploration → targeted external research if useful → synthesis / plan → implementation when wanted.
- Do NOT skip ultrawork unless explicitly instructed.

## Karpathy Discipline
- State assumptions explicitly. If uncertain, ask.
- Minimum code that solves the problem — no speculative abstractions or features.
- Touch only what you must. Every changed line must trace to the request.
- Define success criteria and verify.

## Verification
Run the relevant checks before finishing:
- {{build_commands}}
- {{test_commands}}
- {{targeted_smoke_checks}}

## Final Report
Return:
1. What you changed
2. Assumptions you made
3. Verification results
4. Anything still risky / not done
```

### Quick Variants

**Analysis / review only**
- Add: `Do not edit files. Investigate, verify, and report concrete findings only.`

**Plan first**
- Add: `Do not implement yet. Inspect the repo, propose the minimum viable plan, and identify risks/unknowns.`

**Tight surgical fix**
- Add: `One concern only. No unrelated cleanup, refactors, or formatting drift.`

**Parallelizable work**
- Split into separate spawns only when concerns are truly independent, each with its own scope and verification.

### sessions_spawn Skeleton

```typescript
sessions_spawn({
  task: `{{filled_prompt_template_above}}`,
  runtime: "acp",
  agentId: "opencode",
  mode: "run",
  streamTo: "parent"
})
```

### Ready-to-Use Templates

#### 1) Bug Fix Template

```markdown
[{{timestamp}}] You are fixing a bug in `{{project_path}}`.

## Task Classification
- Type: debug
- Execution mode: implement-now
- OmO mode: use native OmO if helpful

## First Steps
1. Inspect the relevant files and trace the real failure path first.
2. Reproduce or validate the bug before changing code.
3. State assumptions explicitly if any part is unclear.

## Bug
{{bug_description}}

## Scope
- Touch only: {{target_files_or_modules}}
- Do not change: unrelated files, formatting, refactors, adjacent cleanup

## Relevant Context
- Suspected files: {{relevant_files}}
- Observed behavior: {{current_behavior}}
- Expected behavior: {{expected_behavior}}
- Constraints: {{constraints}}

## OmO Instruction (REQUIRED)
- OpenCode is the coding runtime.
- OmO is natively installed inside OpenCode.
- **ALWAYS run `ulw` / `ultrawork`** to activate full discipline agent system.
- Do not skip ultrawork unless explicitly told to.

## Karpathy Discipline
- State assumptions explicitly. If uncertain, ask.
- Minimum code that solves the problem — no speculative abstractions or features.
- Touch only what you must. Every changed line must trace to the bug.
- Define success criteria and verify.

## Success Criteria
- The bug is reproduced or concretely validated.
- The fix addresses the real cause, not just a symptom.
- Relevant checks pass.
- No unrelated code is changed.

## Verification
Run the relevant checks before finishing:
- {{repro_or_targeted_check}}
- {{build_commands}}
- {{test_commands}}

## Final Report
Return:
1. Root cause
2. What you changed
3. Verification results
4. Any residual risk or follow-up needed
```

#### 2) Feature Build Template

```markdown
[{{timestamp}}] You are building a feature in `{{project_path}}`.

## Task Classification
- Type: implement
- Execution mode: implement-now
- OmO mode: use native OmO if helpful

## First Steps
1. Inspect the existing repo patterns and relevant modules first.
2. Identify the minimum path to implement the requested feature.
3. State assumptions and unknowns explicitly before coding.

## Goal
{{feature_goal}}

## Scope
- Build: {{feature_scope}}
- Touch only: {{target_files_or_modules}}
- Do not change: {{out_of_scope_items}}

## Relevant Context
- Existing patterns/files: {{relevant_files}}
- Inputs/outputs: {{interfaces_or_routes}}
- Constraints: {{product_or_technical_constraints}}

## OmO Instruction (REQUIRED)
- OpenCode is the coding runtime.
- OmO is natively installed inside OpenCode.
- **ALWAYS run `ulw` / `ultrawork`** after initial repo inspection.
- Default OmO shape: repo-first exploration → targeted external research if useful → synthesis / plan → implementation.

## Karpathy Discipline
- State assumptions explicitly. If uncertain, ask.
- Minimum code that solves the problem — no speculative abstractions or features.
- Touch only what you must. Every changed line must trace to the feature.
- Define success criteria and verify.

## Success Criteria
- The requested feature works end-to-end within the stated scope.
- Existing behavior outside scope is preserved.
- Relevant checks pass.
- No speculative extras are added.

## Verification
Run the relevant checks before finishing:
- {{build_commands}}
- {{test_commands}}
- {{targeted_smoke_checks}}

## Final Report
Return:
1. What you built
2. Assumptions you made
3. Verification results
4. Anything intentionally deferred or still risky
```

#### 3) Repo Audit / Review Template

```markdown
[{{timestamp}}] You are auditing `{{project_path}}`.

## Task Classification
- Type: review
- Execution mode: analysis-only
- OmO mode: use native OmO if helpful

## Hard Rule
Do not edit files. Investigate, verify, and report concrete findings only.

## Audit Goal
{{audit_goal}}

## Focus Areas
- Review: {{focus_areas}}
- Check files/modules: {{relevant_files}}
- Ignore unless necessary: {{out_of_scope_items}}

## OmO Instruction (REQUIRED)
- OpenCode is the coding runtime.
- OmO is natively installed inside OpenCode.
- **ALWAYS run `ulw` / `ultrawork`** for comprehensive repo exploration and analysis.
- Use OmO's discipline agents for thorough audit coverage.

## Review Standard
- Inspect the actual code, config, and scripts involved.
- Run targeted verification where useful.
- Distinguish confirmed issues from suspicions.
- Prioritize findings by severity and practical impact.

## Verification
Use relevant checks such as:
- {{build_commands}}
- {{test_commands}}
- {{targeted_searches_or_smoke_checks}}

## Final Report
Return:
1. Confirmed findings
2. Severity / priority for each
3. Evidence and affected files
4. Recommended next actions
5. Unknowns that still need verification
```

### Environment Variables for OpenCode
```
OPENCODE_API_KEY="your-api-key-here"
```
Set in `/etc/environment`. Must be present for OpenCode ACP sessions to work.

## Deployment

### Dokploy
- URL: https://main.spidmax.win
- API Key: [set in environment]

### GitHub
- Token: [set in environment]
- Username: jawg-zz
- Email: mosetigeorge@gmail.com
