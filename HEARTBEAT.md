# Heartbeat Configuration

## Background Processing
- Use GPT-OSS 20B model for all tasks (gpt-oss:20b)
- Single unified model approach
- Prefer comprehensive reasoning
- Local-first for low latency

## Periodic Checks
- Check emails (gog)
- Scan Notion workspace
- Update memory index
- Review cron jobs
- **Run context health check** — use `./context-monitor.sh` or check `/status`

## Task Prioritization
- Low-cost tasks first
- Batch similar operations
- Minimize external API calls
- Local model for privacy and speed

## Context Management & Session Health

### Model-Specific Limits & Safety Margins
| Model | Max Tokens | Warning (70%) | Force `/new` (90%) | Buffer Zone |
|-------|------------|---------------|-------------------|-------------|
| **DeepSeek Chat** | 131,072 | 91,750 | 117,965 | **13,107 tokens** |
| **Kimi K2.5** | ~128,000 | 89,600 | 115,200 | 12,800 tokens |
| **Ministral 3** | 256,000 | 179,200 | 230,400 | 25,600 tokens |
| **Qwen 7B** | ~4,000 | 2,800 | 3,600 | 400 tokens |
| **Qwen3 Coder 30B** | ~32,000 | 22,400 | 28,800 | 3,200 tokens |
| **GPT-OSS 20B** | ~8,000 | 5,600 | 7,200 | 800 tokens |

### Automatic Protection Protocol
1. **Check `/status` every 5-10 messages** — manual habit
2. **At WARNING threshold (70%):**
   - Immediately say: "⚠️ Context at [X]% ([Y]/[Z] tokens). Want me to `/new` or spawn this task?"
   - Offer to spawn sub-agent: `sessions_spawn(runtime="subagent", task="...", model="[current-model]")`
3. **At CRITICAL threshold (90%):**
   - **AUTOMATIC `/new`** — no asking, just do it: "🚨 Context at 90% — auto-`/new` to prevent crash"
   - Keep first reply after `/new` to 1-2 sentences
4. **Buffer Zone Protection:**
   - Never exceed `Max Tokens - 10% buffer` (last 10% is emergency-only)
   - DeepSeek: Never go above 118k tokens (90% of 131k)

### Heavy Task Default Behavior
- **Research/analysis** → auto-spawn sub-agent
- **Long outputs** → auto-spawn sub-agent  
- **Multi-step work** → auto-spawn sub-agent
- **File processing** → check file size first, spawn if >50KB

### Crash Prevention Checklist
- [ ] Check `/status` before starting any complex task
- [ ] Estimate token count: ~1 token = 0.75 words (English)
- [ ] 10k tokens ≈ 7,500 words ≈ 15 pages of text
- [ ] When in doubt, spawn
- [ ] **Golden Rule:** Better to spawn 10 times than crash once

## Post-`/new` Protocol
1. After `/new`, keep first reply SHORT (1-2 sentences)
2. Let user send the heavy content as follow-up
3. Qwen 7B context is tiny — treat it like a fresh notepad, not a filing cabinet

## Notification Preferences
- **Gateway Watchdog**: Only notify on failures, not successful checks
- **TKA Email/Grade Checks**: SILENT when empty — only notify when actual content found
- **General Rule**: No "all clear" messages. Only exceptions, errors, or actionable items.

## Warning Triggers
- 15+ messages in session → suggest summarizing or `/new`
- Complex multi-step task incoming → spawn immediately
- User asks for research/analysis → default to isolated session
- "Working on it..." for >30s on local model → consider offloading
