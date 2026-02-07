# Clawdi Infrastructure

> Documentation of an OpenClaw-powered household AI infrastructure with autonomous subagent teams.

## 🏠 Overview

This repository documents a production OpenClaw deployment managing a multi-agent system with specialized AI assistants. The setup demonstrates practical patterns for running persistent AI agents with division of labor, cost optimization, and security scanning.

**Key Components:**
- 🤖 **4 Persistent Subagents** - Specialized autonomous agents
- 💰 **Butler** - API budget manager
- 🧹 **Janitor** - Repository cleanup automation
- 🔒 **Gatekeeper** - Security scanner for leaked secrets
- 📊 **Moderator** - Agent coordinator and reporter

## 📋 Table of Contents

- [Infrastructure Setup](#infrastructure-setup)
- [Subagent Team](#subagent-team)
- [Skills & Capabilities](#skills--capabilities)
- [Workflows](#workflows)
- [Cost Optimization](#cost-optimization)
- [Security Practices](#security-practices)

## 🖥️ Infrastructure Setup

### Platform
- **OS:** Ubuntu 24.04 LTS
- **Hosting:** Hetzner VPS
- **OpenClaw Version:** 2026.2.6-3
- **Memory Backend:** QMD (optimized for low token usage)

### Configuration Highlights
```json
{
  "agents.defaults.maxConcurrent": 1,
  "agents.defaults.subagents.maxConcurrent": 3,
  "memory.backend": "qmd",
  "sessions": "auto-clear every 2h"
}
```

**Token Optimization:**
- Before optimization: 62K tokens/message
- After QMD + config tuning: 13K tokens/message
- Target: <10K tokens/message

## 🤖 Subagent Team

### Architecture

```
Main Agent (Clawdi)
├── butler-agent     (nvidia/kimi-k2.5)
├── janitor-agent    (groq/llama-3.3-70b)
├── gatekeeper-agent (groq/llama-3.3-70b)
└── moderator-agent  (nvidia/kimi-k2.5)
```

### 1. Butler Agent 💰

**Model:** Nvidia Kimi K2.5 (FREE)  
**Role:** API budget & key manager

**Responsibilities:**
- Track token usage across 6 providers (Anthropic, Groq, Nvidia, OpenAI, OpenRouter, OpenCode)
- Recommend optimal models based on task complexity
- Monitor spending in real-time
- Alert before rate limits
- Prevent surprise bills

**Skills:** `/butler`

**Example Usage:**
```bash
sessions_send butler-agent "Recommend a model for: create a landing page"
# → Suggests: groq/llama (FREE, fast)

sessions_send butler-agent "Check current API budget status"
# → Reports usage across all providers
```

### 2. Janitor Agent 🧹

**Model:** Groq Llama 3.3 70B (FREE)  
**Role:** GitHub repository cleanup specialist

**Responsibilities:**
- Scan repos for junk files (node_modules, cache, build artifacts)
- Remove unused files safely
- Detect duplicate files
- Clean up merged branches
- Optimize repo size with git GC

**Skills:** `/janitor`, `github`

**Example Usage:**
```bash
sessions_send janitor-agent "Scan minicarlo/repo for junk files"
# → Identifies: 250MB node_modules, 80MB .cache, 12 merged branches

sessions_send janitor-agent "Optimize minicarlo/repo --dry-run"
# → Shows cleanup plan without making changes
```

### 3. Gatekeeper Agent 🔒

**Model:** Groq Llama 3.3 70B (FREE)  
**Role:** Security scanner for leaked secrets

**Responsibilities:**
- Scan commits for leaked API keys before push
- Detect tokens, passwords, credentials
- Alert on security issues
- Prevent credential exposure to GitHub

**Skills:** `github`

**Detection Patterns:**
- API keys: `sk-...`, `ANTHROPIC_API_KEY=...`
- Tokens: `ghp_...`, `gho_...`, bot tokens
- Passwords: `password=...`, `pwd=...`
- Private keys: `BEGIN PRIVATE KEY`

**Example Usage:**
```bash
sessions_send gatekeeper-agent "Scan staged changes for secrets"
# → Alerts if API keys detected before commit

sessions_send gatekeeper-agent "Audit last 5 commits for leaked credentials"
# → Reviews git history for exposure
```

### 4. Moderator Agent 📊

**Model:** Nvidia Kimi K2.5 (FREE)  
**Role:** Subagent coordinator & reporter

**Responsibilities:**
- Monitor all other subagents
- Aggregate results from multi-agent operations
- Generate daily summary reports
- Coordinate workflows between agents
- Escalate issues to main agent

**Skills:** None (uses `sessions_list`, `sessions_history` via main agent)

**Example Usage:**
```bash
sessions_send moderator-agent "Generate daily report of all subagent activities"
# → Summary of butler budgets, janitor cleanups, gatekeeper scans

sessions_send moderator-agent "Check status of all subagents"
# → Health check across the team
```

## 🛠️ Skills & Capabilities

### Active Skills

| Skill | Purpose | Used By |
|-------|---------|---------|
| **butler** | API budget tracking & model recommendations | butler-agent |
| **janitor** | Repository cleanup automation | janitor-agent |
| **github** | GitHub operations via gh CLI | janitor-agent, gatekeeper-agent |
| **github-secrets-manager** | Secure API key storage in GitHub secrets | Main agent |
| **skill-creator** | Build new AgentSkills | Main agent |

### Skill Structure Example

```
skills/butler/
├── SKILL.md                 # Main documentation
├── scripts/
│   ├── check_budgets.py     # Budget status checker
│   ├── recommend_model.py   # Task→model assignment
│   ├── log_usage.py         # Usage tracking
│   └── rotate_key.py        # Key rotation (stub)
└── references/
    └── API_KEYS.md          # Provider details & limits
```

## 🔄 Workflows

### Daily Maintenance Workflow

**Automated via moderator-agent:**
1. Butler checks API budgets
2. Janitor scans target repos for junk
3. Gatekeeper reviews recent commits
4. Moderator aggregates and reports findings

### Pre-Commit Security Workflow

**Triggered before git push:**
1. Developer stages changes
2. Gatekeeper scans staged files for secrets
3. If secrets found → Alert & block
4. If clean → Proceed to commit

### Cost Optimization Workflow

**Continuous monitoring:**
1. Butler tracks token usage per provider
2. Compares task complexity vs model cost
3. Recommends cheaper alternatives when appropriate
4. Example: "Use Groq Llama instead of Sonnet for this simple task → Save $0.045"

## 💰 Cost Optimization

### Model Selection Strategy

**Free Models (Always prefer for routine tasks):**
- **Groq Llama 3.3 70B** - Fast, 131K context
- **Nvidia Kimi K2.5** - 128K context
- **OpenCode Zen** - 128K context

**Ultra-Cheap ($0.0003/1M tokens):**
- **OpenRouter DeepSeek** - Quick lookups
- **OpenRouter Qwen** - Code generation

**Premium ($3-15/1M tokens):**
- **Anthropic Sonnet 4.5** - Complex reasoning only

### Current Costs

**Monthly spend estimate:**
- 4 Subagents (all free models): **$0.00**
- Main agent (Sonnet for complex tasks): **~$2-5**
- Total: **<$10/month**

**Optimization Tips:**
1. Use butler to recommend models before spawning subagents
2. Reserve Sonnet for explicit complex tasks
3. Batch operations to reduce API calls
4. Monitor via butler's weekly reports

## 🔒 Security Practices

### Secrets Management

**GitHub Secrets (via github-secrets-manager skill):**
```bash
# Store API keys securely
gh secret set ANTHROPIC_API_KEY --body "sk-..." --repo owner/repo

# List secrets (values hidden by GitHub)
gh secret list --repo owner/repo
```

**Never commit:**
- API keys
- Bot tokens
- Passwords
- Private keys
- `.env` files with credentials

**Gatekeeper prevents:**
- Accidental credential commits
- Exposed tokens in code
- Sensitive URLs with auth in query params

### Configuration Security

**Encrypted storage:**
- Credentials: `~/.openclaw/credentials/api-keys.enc.json`
- Master key: `~/.openclaw/credentials/.master.key`
- **Backup the master key** - Cannot decrypt without it!

## 📈 Performance Metrics

### Token Usage (per message)

| Configuration | Tokens/Message |
|--------------|----------------|
| Before optimization | 62,000 |
| After QMD + tuning | 13,000 |
| Target | <10,000 |

**Optimization techniques:**
- QMD memory backend (vs full memory)
- Auto-clear sessions every 2h
- Selective context injection
- Compact workspace files

### Subagent Efficiency

| Agent | Model | Cost/Run | Avg Runtime |
|-------|-------|----------|-------------|
| butler | Nvidia Kimi | $0.00 | 10-30s |
| janitor | Groq Llama | $0.00 | 30-90s |
| gatekeeper | Groq Llama | $0.00 | 15-45s |
| moderator | Nvidia Kimi | $0.00 | 5-20s |

## 🚀 Getting Started

**Prerequisites:**
- Ubuntu 24.04+ (or similar Linux)
- OpenClaw 2026.2+
- GitHub CLI (`gh`) installed and authenticated
- API keys for desired providers

**Basic Setup:**
1. Install OpenClaw
2. Configure providers (Anthropic, Groq, Nvidia, etc.)
3. Install skills from this repo
4. Spawn persistent subagents with `cleanup="keep"`
5. Configure cron jobs for daily maintenance

**Detailed guides coming soon!**

## 📚 Resources

- [OpenClaw Documentation](https://docs.openclaw.ai)
- [ClawHub - Skill Repository](https://clawhub.com)
- [OpenClaw GitHub](https://github.com/openclaw/openclaw)
- [OpenClaw Discord](https://discord.com/invite/clawd)

## 🤝 Contributing

This is a documentation repository showcasing a real-world OpenClaw deployment. If you have questions, suggestions, or want to share your own setup:

- Open an issue for discussion
- Submit a PR with improvements to documentation
- Share your own household AI infrastructure patterns

## 📄 License

MIT License - Feel free to learn from and adapt these patterns for your own AI infrastructure.

---

**Note:** This repository documents infrastructure patterns, not deployable code. API keys, credentials, and sensitive configuration have been intentionally excluded. Use this as a reference for building your own OpenClaw-powered multi-agent system.
