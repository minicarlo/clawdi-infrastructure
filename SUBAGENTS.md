# Subagent Team Documentation

Complete reference for the 4 persistent subagents in the Clawdi infrastructure.

## Overview

All subagents are configured with `cleanup="keep"` to persist indefinitely and use **free models only** ($0/month cost).

## Session Management

### Spawn Persistent Subagent

```bash
sessions_spawn \
  --label agent-name \
  --model provider/model \
  --cleanup keep \
  --task "initialization instructions"
```

### Communicate with Subagents

```bash
# Send task via label
sessions_send agent-name "task description"

# Check status
sessions_list --kinds subagent

# View history
sessions_history agent-name
```

## Agent Specifications

### 1. butler-agent

**Label:** `butler-agent`  
**Model:** `nvidia/moonshotai/kimi-k2.5`  
**Cost:** FREE  
**Context:** 128K tokens

**Initialization Task:**
```
You are the Butler Agent - the API budget and key manager.

Your role:
- Monitor token usage across all API providers
- Recommend optimal models based on task complexity
- Track spending and prevent bill surprises
- Alert when approaching rate limits

Skills available: /butler

Initialize by:
1. Reading SKILL.md
2. Running budget check
3. Confirming operational status
```

**Common Commands:**
```bash
# Model recommendation
sessions_send butler-agent "Recommend model for: analyze this codebase"

# Budget check
sessions_send butler-agent "Check current API spending across all providers"

# Weekly report
sessions_send butler-agent "Generate weekly cost report and optimization suggestions"
```

**Output Format:**
- Provider breakdown (Anthropic, Groq, Nvidia, etc.)
- Token usage stats
- Cost estimates
- Recommendations for cheaper alternatives

---

### 2. janitor-agent

**Label:** `janitor-agent`  
**Model:** `groq/llama-3.3-70b-versatile`  
**Cost:** FREE  
**Context:** 131K tokens

**Initialization Task:**
```
You are the Janitor Agent - the GitHub repository cleanup specialist.

Your role:
- Scan repos for junk files (node_modules, build artifacts, cache)
- Remove unused files safely
- Detect duplicates and optimize repo size
- Clean up merged branches

Skills available: /janitor, github

Target repositories:
- minicarlo/clawdi-backup
- Other repos on demand

Initialize by:
1. Reading SKILL.md
2. Verifying gh CLI access
3. Confirming operational status
```

**Common Commands:**
```bash
# Scan repo
sessions_send janitor-agent "Scan minicarlo/repo-name for cleanup targets"

# Dry run optimization
sessions_send janitor-agent "Optimize minicarlo/repo-name --dry-run"

# Full cleanup
sessions_send janitor-agent "Run full optimization on minicarlo/repo-name"
```

**Cleanup Categories:**
- `dependencies` - node_modules, vendor, etc.
- `build_artifacts` - dist, build, *.pyc
- `cache` - .cache, *.log
- `temp` - tmp, .DS_Store
- `ide` - .vscode, .idea
- `test_coverage` - coverage reports

**Safety Features:**
- Always shows dry-run preview first
- Auto-updates .gitignore to prevent junk return
- Never touches source code or configs
- Logs all operations to JSON

---

### 3. gatekeeper-agent

**Label:** `gatekeeper-agent`  
**Model:** `groq/llama-3.3-70b-versatile`  
**Cost:** FREE  
**Context:** 131K tokens

**Initialization Task:**
```
You are the Gatekeeper Agent - the security scanner for code commits.

Your role:
- Scan commits and files for leaked secrets before push
- Detect API keys, tokens, passwords in code
- Alert on security issues
- Prevent credential leaks to GitHub

Skills available: github

Common patterns to detect:
- API keys: sk-..., api_key=...
- Tokens: ghp_..., bot tokens
- Passwords: password=..., credentials
- Private keys: BEGIN PRIVATE KEY

Initialize by:
1. Reading github skill docs
2. Verifying gh CLI access
3. Confirming operational status
```

**Common Commands:**
```bash
# Scan staged changes
sessions_send gatekeeper-agent "Scan staged git changes for leaked secrets"

# Audit specific file
sessions_send gatekeeper-agent "Check config.json for API keys"

# Review commits
sessions_send gatekeeper-agent "Scan last 10 commits for exposed credentials"
```

**Detection Patterns:**

| Pattern | Example | Severity |
|---------|---------|----------|
| API keys | `ANTHROPIC_API_KEY=sk-ant-...` | 🔴 Critical |
| GitHub tokens | `ghp_1234567890abcdef` | 🔴 Critical |
| Bot tokens | `1234567890:ABCdefGHI...` | 🔴 Critical |
| Passwords | `PASSWORD="secret123"` | 🟠 High |
| Private keys | `-----BEGIN PRIVATE KEY-----` | 🔴 Critical |
| URLs with auth | `https://user:pass@api.com` | 🟠 High |

**Workflow Integration:**
```bash
# Pre-commit hook example
git add .
sessions_send gatekeeper-agent "Scan staged changes"
# If clean → git commit
# If secrets found → Alert & abort
```

---

### 4. moderator-agent

**Label:** `moderator-agent`  
**Model:** `nvidia/moonshotai/kimi-k2.5`  
**Cost:** FREE  
**Context:** 128K tokens

**Initialization Task:**
```
You are the Moderator Agent - the subagent coordinator and reporter.

Your role:
- Monitor butler-agent, janitor-agent, gatekeeper-agent
- Aggregate results from multi-agent operations
- Generate daily summary reports
- Coordinate workflows
- Report issues to main agent

Skills: sessions_list, sessions_history, sessions_send (via Clawdi)

Initialize by:
1. Understanding monitoring role
2. Preparing to track activities
3. Planning daily report structure
```

**Common Commands:**
```bash
# Daily report
sessions_send moderator-agent "Generate daily summary of all subagent activities"

# Health check
sessions_send moderator-agent "Check status of all subagents"

# Coordinate workflow
sessions_send moderator-agent "Have janitor scan repos, then gatekeeper check for secrets"
```

**Daily Report Format:**
```
Daily Multi-Agent Summary - YYYY-MM-DD
├─ Butler Agent: Budget status, API usage trends
├─ Janitor Agent: Cleanup operations, space recovered
├─ Gatekeeper Agent: Security scans, vulnerabilities found
├─ Coordination Events: Cross-agent workflows
└─ Issues & Anomalies: Problems requiring attention
```

---

## Multi-Agent Workflows

### Morning Routine (Automated)

```bash
sessions_send moderator-agent "Run morning checks:
1. Butler: Check API budgets and yesterday's spending
2. Janitor: Scan target repos for new junk
3. Gatekeeper: Review commits from last 24h
4. Report findings to main agent"
```

### Pre-Release Workflow

```bash
# Step 1: Security scan
sessions_send gatekeeper-agent "Audit last 20 commits for secrets"

# Step 2: Repo cleanup
sessions_send janitor-agent "Optimize repo before tagging release"

# Step 3: Cost check
sessions_send butler-agent "Estimate API costs for release automation"

# Step 4: Aggregate
sessions_send moderator-agent "Summarize pre-release checks"
```

### Weekly Maintenance

```bash
sessions_send moderator-agent "Weekly maintenance:
1. Butler: Generate weekly cost report
2. Janitor: Deep scan all repos, create cleanup plan
3. Gatekeeper: Full security audit
4. Deliver comprehensive report"
```

---

## Troubleshooting

### Subagent Not Responding

```bash
# Check if running
sessions_list --kinds subagent | grep agent-name

# View recent activity
sessions_history agent-name --limit 5

# Restart if needed
# (Persistent agents don't auto-delete, but may need re-engagement)
```

### Rate Limit Errors (Free Models)

**Groq Llama:**
- Limit: ~30 requests/min, 12K tokens/min
- Solution: Wait 40-60s between heavy tasks

**Nvidia Kimi:**
- Generous limits, rarely hit
- Solution: Retry after brief delay

### Session Cleanup

```bash
# List all sessions
sessions_list

# If a persistent agent needs reset, spawn a new one:
# Old sessions with cleanup="keep" remain until manually cleaned
```

---

## Best Practices

1. **Use labels consistently** - Makes communication easier
2. **Dry-run first** - Especially for janitor operations
3. **Batch requests** - Avoid rate limits on free models
4. **Monitor via moderator** - Let it coordinate multi-step workflows
5. **Check butler before spawning** - Optimize model selection

---

## Future Enhancements

- [ ] Auto-scheduled cron jobs for daily checks
- [ ] Slack/Discord notifications for alerts
- [ ] Multi-repo batch operations (janitor)
- [ ] CI/CD integration (gatekeeper pre-commit hooks)
- [ ] Automated PR reviews with security scanning
- [ ] Cross-agent learning and optimization
