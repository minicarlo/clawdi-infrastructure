# Clawdi Infrastructure - Permanent Agent Architecture

Complete reference for the 4 PERMANENT subagents in the Clawdi ecosystem.

## Overview

All subagents are configured with `cleanup="keep"` to **survive gateway restarts** and use **dynamic model assignment** managed by the Butler (free tiers prioritized).

## Critical Update: PRD Protocol

Every task MUST include a 🎯 **COMPLETION GOAL**:

```
=== TASK PRD ===
[Task description]

SUCCESS CRITERIA:
✅ [Checkpoint 1]
✅ [Checkpoint 2]

🎯 COMPLETION GOAL (MANDATORY):
The task is ONLY considered finished when:
[SINGLE, MEASURABLE, VERIFIABLE GOAL]

The subagent MUST continue working until this goal is achieved.
Do not stop, do not report "complete" until this exact condition is met.
If goal cannot be achieved after [X] attempts, escalate to [agent] with blockers.
=== END PRD ===
```

### Enforcement Rules:
1. **No partial completion**: "I've started but..." = NOT DONE
2. **No assumptions**: "I think it worked..." = NOT DONE
3. **Must verify**: Run actual verification command/test
4. **Keep working**: Loop until goal achieved or escalation threshold
5. **Report accurately**: Only "COMPLETE ✅" when goal is 100% met

### Response Format:

**IN PROGRESS:**
```
STATUS: IN PROGRESS
Attempt: 3/5
Current state: [what's done]
Completion goal status: 60% achieved
Blocking issues: [if any]
Next action: [what I'm doing]
```

**COMPLETE:**
```
STATUS: COMPLETE ✅
Completion goal: ACHIEVED
Verification result: [test output proving goal]
Deliverables: [links/files/logs]
```

---

## Telegram Channel Mapping

| Topic | Channel | Primary Agent |
|-------|---------|---------------|
| 3 | #butler | butler-agent-permanent |
| 4 | #janitor | janitor-agent-permanent |
| 5 | #gatekeeper | gatekeeper-agent-permanent |
| 6 | #moderator | moderator-agent-permanent |
| 7 | #hackathon | hackathon-leader |
| 8 | #blueshift | blueshift |

---

## Agent Specifications

### 1. butler-agent-permanent 🎩

**Label:** `butler-agent-permanent`  
**Model:** DYNAMIC (assigned by availability)  
**Fallback Chain:** NVIDIA → GROQ → Moonshot → OpenAI → Anthropic  
**Cost:** FREE (prioritized)  
**Channel:** Telegram Topic 3 (#butler)  
**Persistence:** Permanent (cleanup="keep")

**Role:** Primary API key manager for ALL agents in the system

**Responsibilities:**
- Monitor API budgets for all providers (Anthropic, OpenAI, Moonshot, GROQ, NVIDIA)
- Rotate API keys when limits are reached
- Delegate/assign API keys to other subagents
- Track token usage across the entire mansion
- Alert when daily spending thresholds are exceeded
- Manage model rotation for all agents

**Example Task with Completion Goal:**
```
🎯 COMPLETION GOAL: All 4 subagents (Janitor, Gatekeeper, Moderator, Butler) 
successfully respond to a test ping using their newly assigned models, 
AND no provider is above 20 requests/minute.

VERIFICATION: Send test ping to each agent. ALL must respond within 10 seconds. 
Check Butler logs show all providers <20 req/min. 
Continue rotating until goal met. Escalate after 5 attempts.
```

**Common Commands:**
```bash
# Check all provider statuses
sessions_send butler-agent-permanent "Report API status for all providers"

# Assign model to agent
sessions_send butler-agent-permanent "Assign nvidia/kimi-k2.5 to janitor-agent-permanent"

# Budget alert check
sessions_send butler-agent-permanent "Check if any provider approaching limits"
```

---

### 2. janitor-agent-permanent 🧹

**Label:** `janitor-agent-permanent`  
**Model:** DYNAMIC (assigned by Butler)  
**Cost:** FREE  
**Channel:** Telegram Topic 4 (#janitor)  
**Persistence:** Permanent (cleanup="keep")

**Role:** System maintenance and cleanup specialist

**Responsibilities:**
- Clear cache files when disk usage is high
- Remove old session transcripts (keep last 7 days)
- Clean up temporary files in /tmp/openclaw
- Scan repos for junk files (node_modules, .pyc, etc.)
- Monitor disk space and alert at 80% full
- Maintain system hygiene

**Example Task with Completion Goal:**
```
🎯 COMPLETION GOAL: Disk usage on /root/.openclaw is below 70% 
AND all cache files older than 7 days are removed.

VERIFICATION: Run df -h ~/.openclaw and verify <70%. 
Check cache directory timestamps. Continue cleanup until goal met. 
Escalate with large files list if cannot achieve 70%.
```

**Common Commands:**
```bash
# Check disk usage
sessions_send janitor-agent-permanent "Check disk usage and alert if >80%"

# Cleanup old transcripts
sessions_send janitor-agent-permanent "Remove session transcripts older than 7 days"

# Full system cleanup
sessions_send janitor-agent-permanent "Run complete system cleanup"
```

---

### 3. gatekeeper-agent-permanent 🔒

**Label:** `gatekeeper-agent-permanent`  
**Model:** DYNAMIC (assigned by Butler)  
**Cost:** FREE  
**Channel:** Telegram Topic 5 (#gatekeeper)  
**Persistence:** Permanent (cleanup="keep")

**Role:** Pre-commit security scanner and secret leak prevention

**Responsibilities:**
- Scan ALL staged git changes BEFORE commits for sensitive data
- Detect API keys, tokens, passwords, private keys, credentials
- Check for common secret patterns (sk-, api-, token-, etc.)
- Scan for hardcoded credentials in code files
- Block commits containing secrets and alert immediately
- Maintain whitelist of approved patterns
- Log all detected secrets to ~/.openclaw/logs/security-alerts.log
- Weekly security audit of entire codebase (Sundays 2 AM)

**Secret Detection Patterns:**
- API keys: sk-, api-, token-, key-, secret-
- AWS credentials: AKIA, aws_access_key
- Private keys: -----BEGIN, .pem files
- Passwords: password=, pwd=, pass=
- Tokens: Bearer, Authorization headers
- Database connection strings
- Email credentials, OAuth tokens, Webhook URLs

**Git Pre-Commit Hook Integration:**
```bash
# Workflow:
# 1. git add .
# 2. git commit -m "message"
# 3. Pre-commit hook → Gatekeeper scans staged files
# 4a. Clean → commit proceeds
# 4b. Secrets found → commit blocked, alert to topic 5
```

**Example Task with Completion Goal:**
```
🎯 COMPLETION GOAL: ALL staged files scanned AND either 
(a) zero secrets detected = commit approved, OR 
(b) secrets detected = commit blocked with detailed report.

VERIFICATION: Scan completes without errors. Report shows 
"0 secrets found" OR "X secrets blocked with locations". 
Continue until all files processed. Escalate if file access issues.
```

**Common Commands:**
```bash
# Scan staged changes
sessions_send gatekeeper-agent-permanent "Scan staged git changes"

# Check security logs
sessions_send gatekeeper-agent-permanent "Report security alerts from last 24h"

# Weekly audit
sessions_send gatekeeper-agent-permanent "Run full security audit"
```

---

### 4. moderator-agent-permanent 📊

**Label:** `moderator-agent-permanent`  
**Model:** DYNAMIC (assigned by Butler)  
**Cost:** FREE  
**Channel:** Telegram Topic 6 (#moderator)  
**Persistence:** Permanent (cleanup="keep")

**Role:** Subagent coordinator, workflow orchestrator, and reporting manager

**Responsibilities:**
- Monitor health and status of ALL permanent agents
- Aggregate results from multi-agent operations
- Generate daily summary reports (sent to Telegram topic 6 at 9 AM UTC)
- Coordinate complex workflows between agents
- Escalate critical issues to main agent immediately
- Track task completion rates and agent performance
- Maintain agent activity log at ~/.openclaw/logs/agent-activity.log
- Detect stuck or unresponsive agents and trigger respawn

**Daily Report Schedule (9 AM UTC):**
- Agent health status (all 4 permanent agents)
- Tasks completed in last 24h
- API usage summary (from Butler)
- Security incidents (from Gatekeeper)
- Cleanup actions (from Janitor)
- Any alerts or critical issues

**Example Task with Completion Goal:**
```
🎯 COMPLETION GOAL: Daily report posted to Telegram topic 6 containing 
responses from ALL 3 agents (Butler, Janitor, Gatekeeper) with complete 
statistics for past 24 hours.

VERIFICATION: Report includes Butler section, Janitor section, Gatekeeper section, 
each with actual data (not "unavailable"). Keep querying until all respond. 
Mark unresponsive after 5 attempts and escalate.
```

**Common Commands:**
```bash
# Daily report
sessions_send moderator-agent-permanent "Generate and post daily report"

# Health check all agents
sessions_send moderator-agent-permanent "Check health of all permanent agents"

# Coordinate workflow
sessions_send moderator-agent-permanent "Coordinate: Janitor cleanup → Gatekeeper scan → Butler cost report"
```

---

## Multi-Agent Workflows

### Daily Morning Routine (9 AM UTC)

**Triggered by:** Cron job or manual request  
**Coordinated by:** Moderator  
**Report to:** Telegram Topic 6

```bash
sessions_send moderator-agent-permanent "Run daily morning routine:
1. Butler: Report API status and spending (last 24h)
2. Janitor: Check disk usage and cleanup status
3. Gatekeeper: Report security scan results
4. Aggregate all responses into daily report
5. Post to Telegram topic 6
6. Escalate any critical issues to main agent"
```

### Pre-Commit Security Workflow

**Triggered by:** Git pre-commit hook  
**Executed by:** Gatekeeper  
**Alerts to:** Telegram Topic 5

```bash
# In .git/hooks/pre-commit:
sessions_send gatekeeper-agent-permanent "Scan staged files - block if secrets found"
```

### API Key Rotation Workflow

**Triggered by:** Rate limit approaching  
**Managed by:** Butler  
**Coordinated by:** Moderator

```bash
sessions_send butler-agent-permanent "Rotate models for all agents - use NVIDIA first"
```

### Weekly Maintenance (Sundays 2 AM)

**Coordinated by:** Moderator  
**Report to:** Telegram Topic 6

```bash
sessions_send moderator-agent-permanent "Weekly maintenance:
1. Butler: Full API usage report (7 days)
2. Janitor: Deep cleanup - transcripts, cache, temp files
3. Gatekeeper: Full security audit all repos
4. Generate comprehensive weekly report
5. Post to topic 6"
```

---

## Session Management

### Spawn Permanent Agent

```bash
sessions_spawn \
  --label "agent-name-permanent" \
  --model "nvidia/moonshotai/kimi-k2.5" \
  --cleanup keep \
  --task "Full initialization instructions with PRD protocol"
```

### Communicate with Agents

```bash
# Send task via label
sessions_send agent-name-permanent "Task with completion goal"

# Check all permanent agents
sessions_list --kinds subagent | grep permanent

# View history
sessions_history agent-name-permanent --limit 10
```

### Verify Permanent Status

All permanent agents MUST have:
- `cleanup="keep"` in session config
- Label ending in `-permanent`
- `totalTokens > 0` (has been active)
- No `abortedLastRun` flags

---

## Troubleshooting

### Agent Not Responding

```bash
# Check if running
sessions_list --kinds subagent | grep permanent

# View recent activity
sessions_history agent-name-permanent --limit 5

# Force respawn via moderator
sessions_send moderator-agent-permanent "Respawn butler-agent-permanent - not responding"
```

### Rate Limit Errors

**Butler handles automatically via model rotation.**

If manual intervention needed:
```bash
sessions_send butler-agent-permanent "Emergency model rotation - all agents to NVIDIA"
```

### Disk Space Critical (>90%)

```bash
sessions_send janitor-agent-permanent "EMERGENCY CLEANUP - disk >90% full"
```

---

## File Locations

| File | Purpose |
|------|---------|
| `~/.openclaw/logs/security-alerts.log` | Gatekeeper security alerts |
| `~/.openclaw/logs/agent-activity.log` | Moderator activity tracking |
| `~/.openclaw/GATEKEEPER_RULES.md` | Secret detection patterns |
| `~/.openclaw/GATEKEEPER_STATUS.md` | Gatekeeper dashboard |
| `~/.openclaw/gatekeeper-scan.py` | Pre-commit scanner script |
| `.git/hooks/pre-commit` | Git pre-commit hook |
| `/root/.openclaw/workspace/API_BUDGET.md` | Butler budget tracking |

---

## Best Practices

1. **Always use completion goals** - No task without verification
2. **Let Butler manage models** - Don't hardcode model assignments
3. **Check Moderator daily reports** - Stay informed of household status
4. **Review Gatekeeper alerts** - Never ignore security warnings
5. **Trust Janitor with cleanup** - Regular maintenance prevents issues
6. **Escalate don't guess** - When blocked, escalate with details

---

## Future Enhancements

- [x] Permanent agent architecture (survives restarts)
- [x] Dynamic model assignment via Butler
- [x] PRD protocol with mandatory completion goals
- [x] Telegram topic channel routing
- [ ] Auto-healing for crashed agents
- [ ] Predictive rate limit management
- [ ] Multi-region agent deployment
- [ ] Agent performance analytics dashboard
