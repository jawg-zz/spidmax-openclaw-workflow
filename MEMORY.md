# MEMORY.md - Long-Term Memory

## User

- **Name:** George
- **Timezone:** Africa/Nairobi (EAT, UTC+3)
- **Prefers:** webchat via openclaw-control-ui
- **Agent preference:** always use ulw/ultrawork for coding tasks (OmO required)
- Started working together: 2026-03-06

## Active Projects

See `memory/projects/` for project-specific details:
- `isp-billing.md` - ISP billing system (90% complete, ready for production)

## Agent Orchestration

See `memory/agent-lessons.md` for detailed agent delegation patterns and lessons learned.

Quick reference:
- **NEVER run OpenCode via CLI** for coding work — use `sessions_spawn` with `runtime: "acp"` and `agentId: "opencode"`
- **Inspect first, then delegate** — repo-first investigation comes before any coding spawn
- Use OpenCode for all app coding tasks
- Always use `streamTo: "parent"` for ACP agents
- George prefers **aggressive** automatic use of **oh-my-openagent** when useful
- **OmO is natively installed inside OpenCode** via the `oh-my-openagent` plugin; it is not just a prompting convention here
- **ALWAYS use `ulw` / `ultrawork`** for every coding task (as of 2026-04-23)
- Default OmO workflow: **repo-first exploration → targeted external research if useful → synthesis / planning → implementation when wanted**
- One focused spawn per concern; parallelize only when work is truly independent

## Infrastructure

- **Workspace:** `/data/workspace` (env var `OPENCLAW_WORKSPACE_DIR`)
- **Config:** `/data/.openclaw/openclaw.json`
- **Gateway:** loopback only (`127.0.0.1:18789`), behind reverse proxy at `https://claw.spidmax.win`
- **Model:** `opencode-go/minimax-m2.7` (via OpenCode ACP spawn)
- **OpenCode:** v1.3.17 at `/data/npm-global/lib/node_modules/opencode-ai/bin/opencode`
- **OmO:** `oh-my-openagent@latest` plugin with all discipline agents mapped to `opencode-go/minimax-m2.7`
- **Coding:** OpenCode ACP spawn is the ONLY coding path — native subagents disabled
- **Firecrawl:** High resource overhead (4 CPU / 8GB RAM). Deploy as a separate Dokploy project. Traefik requires explicit routing setup for the API to respond correctly on subdomains.

## Quick Tips

- Deploy via Dokploy UI (George triggers manually)
- Use `github-dokploy-deploy` skill for deployments
- Screenshot reading accuracy is limited — confirm details when precision matters
- MikroTik scripts: no hyphens in pool names (use `hotspotpool` not `hotspot-pool`)

## Coding Rule (CANONICAL - updated 2026-04-23)

**This is the single source of truth for our coding workflow.**

1. You give me the task
2. I spawn OpenCode+OmO with the task
3. OmO handles everything: classify, inspect, research, plan, implement, verify, review
4. I report back to you

That's it.

*See TOOLS.md for spawn templates, AGENTS.md for technical details, SOUL.md for my role definition.*


## Dokploy API

- **Auth header:** `x-api-key` (NOT `Authorization: Bearer`)
- **API key:** [set in environment]
- **Projects:** [configure in Dokploy]

## Ecommerce App (isp-billing adjacent)


**M-Pesa callback authentication:** Do NOT require custom auth headers — M-Pesa doesn't send them. Use IP whitelist only. Behind reverse proxy, client IP is in `X-Forwarded-For`, not direct connection. Set `MPESA_IP_WHITELIST=false` in docker-compose.yml `environment` section explicitly.

**Radius Configuration:** `RadiusConfig.customerId` is optional (nullable) to support anonymous hotspot purchases. RADIUS sync to `radcheck` table is non-fatal; it warns and continues if the table doesn't exist.

**Next.js image optimization:** In 14.1.0 containerized builds, `images.domains` and `images.remotePatterns` may fail with "url parameter is not allowed". Use `images.unoptimized: true` as fallback. User must hard-refresh to clear cached `/_next/image` requests.


**MikroTik pool naming:** No hyphens (use `hotspotpool` not `hotspot-pool`) — from RouterOS script lessons. Quote keys/values containing `/` or `+` (e.g. WireGuard keys) to avoid parser errors. Keep single command lines under 254 characters.

## Promoted From Short-Term Memory (2026-04-19)

<!-- openclaw-memory-promotion:memory:memory/2026-04-12.md:273:273 -->
- - Candidate: Possible Lasting Truths: No strong candidate truths surfaced. [score=0.845 recalls=0 avg=0.620 source=memory/2026-04-12.md:13-13]
<!-- openclaw-memory-promotion:memory:memory/2026-04-13.md:338:341 -->
- - Candidate: Reflections: Theme: `assistant` kept surfacing across 798 memories.; confidence: 1.00; evidence: memory/.dreams/session-corpus/2026-04-09.txt:3-3, memory/.dreams/session-corpus/2026-04-09.txt:5-5, memory/.dreams/session-corpus/2026-04-09.txt:7-7; note: reflection - confidence: 0.00 - evidence: memory/2026-04-13.md:338-341 - recalls: 0 [score=0.838 recalls=0 avg=0.620 source=memory/2026-04-13.md:3-6]
<!-- openclaw-memory-promotion:memory:memory/2026-04-13.md:342:345 -->
- - Candidate: Reflections: Theme: `user` kept surfacing across 539 memories.; confidence: 0.79; evidence: memory/.dreams/session-corpus/2026-04-09.txt:1-1, memory/.dreams/session-corpus/2026-04-09.txt:2-2, memory/.dreams/session-corpus/2026-04-09.txt:4-4; note: reflection - confidence: 0.00 - evidence: memory/2026-04-13.md:342-345 - recalls: 0 [score=0.838 recalls=0 avg=0.620 source=memory/2026-04-13.md:8-11]
<!-- openclaw-memory-promotion:memory:memory/2026-04-13.md:348:348 -->
- - Candidate: Possible Lasting Truths: No strong candidate truths surfaced. [score=0.838 recalls=0 avg=0.620 source=memory/2026-04-13.md:83-83]

## Promoted From Short-Term Memory (2026-04-20)

<!-- openclaw-memory-promotion:memory:memory/2026-04-15.md:5:5 -->
- Hotspot purchase flow confirmed working end-to-end: [score=0.854 recalls=0 avg=0.620 source=memory/2026-04-15.md:5-5]
