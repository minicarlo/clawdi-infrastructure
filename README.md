# 🏛️ THE CLAWDI MANSION - AI Agent Architecture

> *"Cheap model runs the mansion. Expensive models get hired for shifts. The Butler manages the payroll. The Gatekeeper locks the doors."*

**You don't have a budget problem. You have an architecture problem.**

---

## 🏛️ The Philosophy

This repository documents **The Clawdi Mansion** — a revolutionary AI agent architecture that scales to 15+ parallel agents without scaling costs.

### The Core Principle
- **Cheap model runs the mansion** (orchestration, authority, delegation)
- **Expensive models get hired for shifts** (task-specific work)
- **The Butler manages the payroll** (token budgets, API rotation)
- **The Gatekeeper locks the doors** (security, GitHub protection)

**Result:** 15+ parallel agents, $0-2/day in API costs.

---

## 👑 Meet Clawdi — The Patriarch

Clawdi doesn't do the heavy lifting. Clawdi **orchestrates**.

**Role:** Master of the House, family head, delegator, decision-maker  
**API:** Moonshot Kimi (CHEAP — $0.50-2.00/M tokens)  
**Function:** Authority, coordination, monitoring — not intelligence

> *"Why? Because orchestration doesn't need intelligence. It needs authority."*

Clawdi delegates to specialized capabilities and spawns expensive subagents only for specific tasks.

---

## 🏛️ The Mansion Structure

```
┌─────────────────────────────────────────────────────────────────┐
│  CASE (Estate Owner)                                           │
│  Strategic Vision & Legacy Planning                             │
└─────────────────────────┬───────────────────────────────────────┘
                          │
                          ▼
┌─────────────────────────────────────────────────────────────────┐
│  CLAWDI (The Patriarch)                                         │
│  Orchestration • Authority • Delegation                         │
│  Runs on CHEAP API (Moonshot Kimi)                              │
└─────────────────────────┬───────────────────────────────────────┘
                          │
          ┌───────────────┼───────────────┐
          ▼               ▼               ▼
   ┌─────────────┐ ┌─────────────┐ ┌─────────────┐
   │  MODERATOR  │ │ GATEKEEPER  │ │   BUTLER    │
   │ Floor Watch │ │   GitHub    │ │ Token CFO   │
   │   & Reports │ │  Security   │ │             │
   └─────────────┘ └─────────────┘ └─────────────┘
          │
          ▼
   ┌─────────────┐
   │  JANITOR    │
   │Mimi Wing    │
   │Maintenance  │
   └─────────────┘
```

---

## 🎩 The Butler (CFO) — Token Budget Manager

**The backbone of the mansion.**

Money = Tokens. The Butler manages token budgets across 10+ API keys from 6 different providers. When a sub-agent spawns for a task, it doesn't just pick any key. It goes to the Butler.

### The Butler's Priority Ledger

```
┌──────────────────────────────────────────────────────────────┐
│  BUTLER'S PRIORITY LEDGER                                    │
├──────────────────────────────────────────────────────────────┤
│  1. 🥇 Moonshot Kimi  (Primary/Cheap)    │
│  2. 🥈 NVIDIA Kimi    (Secondary/Free)   │
│  3. 🥉 Groq           (When functional)  │
│  4. 🔶 OpenRouter     (Diversity)        │
│  5. 🟣 Anthropic      (High-value tasks) │
│  6. 🟢 OpenAI         (Premium jobs)     │
└──────────────────────────────────────────────────────────────┘
```

### The Butler's Workflow

```
┌──────────────┐
│   REQUEST    │
│ (Sub-agent)  │
└──────┬───────┘
       │
       ▼
┌──────────────────────────────────────┐
│  BUTLER EVALUATES                    │
│  • Task complexity                   │
│  • Token estimate                    │
│  • Available keys                    │
└──────┬───────────────────────────────┘
       │
       ▼
┌─────────────┐    ┌──────────────┐
│  ALLOCATE   │───►│   ASSIGN     │
│  Right Key  │    │   API Key    │
└─────────────┘    └──────┬───────┘
                          │
                          ▼
                   ┌──────────────┐
                   │   MONITOR    │
                   │ Token Usage  │
                   │  Real-time   │
                   └──────┬───────┘
                          │
                          ▼
                   ┌──────────────┐
                   │ AUTO-ROTATE  │
                   │   @ 80%      │
                   └──────────────┘
```

**Result:** No surprise bills. No crashed agents mid-task. Clean, automated budget allocation.

---

## 🛡️ The Gatekeeper — GitHub Security

Nothing touches GitHub without passing through the Gatekeeper.

**Function:** Pre-commit security scanning
**Scans for:**
- Leaked API keys
- Sensitive data
- Credentials
- Anything that shouldn't be public

### Gatekeeper Protocol

```
Developer          Gatekeeper         Public Repos         Archive
     │                  │                  │                  │
     │ 1. Commit        │                  │                  │
     │─────────────────►│                  │                  │
     │                  │                  │                  │
     │                  │ 2. SCAN          │                  │
     │                  │ (Security Check) │                  │
     │                  │                  │                  │
     │◄─────────────────│                  │                  │
     │ ❌ BLOCKED        │                  │                  │
     │ ✅ APPROVED       │                  │                  │
     │                  │                  │                  │
     │ 3. Push ───────────────────────────►│                  │
     │                  │                  │                  │
     │                  │ 4. Backup ──────────────────────────►│
```

**Security isn't a checklist. It's an agent with veto power.**

---

## 🧹 The Janitor (Mimi Wing) — Estate Maintenance

After the work is done, the mess remains. The Janitor handles all cleanup:

- Cache files
- Unused artifacts
- Dead code
- Repo maintenance

### Janitor Operations

```
┌─────────────────────────────────────────────────────────────┐
│                    JANITOR OPERATIONS                       │
│                      (Mimi Wing)                            │
└─────────────────────────────────────────────────────────────┘

Daily Tasks              Weekly Tasks
     │                        │
     ▼                        ▼
┌─────────────┐        ┌─────────────┐
│ Clear Cache │        │Deep Cleanup │
│Remove Unused│        │Archive Old  │
│ Artifacts   │        │ Branches    │
└─────────────┘        └─────────────┘
```

**Every push is lean. Every repo is sharp.**

---

## 📊 The Moderator — Floor Manager

While 15+ agents run in parallel, someone watches the floor.

**Function:**
- Monitors all sub-agent activity
- Aggregates results
- Code review on completed tasks
- Compiles daily report

### Moderator Dashboard

```
┌──────────────────────────────────────────────────────────┐
│                    MODERATOR DASHBOARD                   │
└──────────────────────────────────────────────────────────┘

Sub-Agent 1 ──┐
Sub-Agent 2 ──┤
Sub-Agent 3 ──┼──► MODERATOR ──► Daily Report ──► CLAWDI
    ...       │   (Watches)      (Compiled)      (Reviews)
Sub-Agent 15 ─┘

That report goes to Clawdi. Clawdi reports to Carlo.
```

**You don't babysit agents. You read summaries from the Patriarch.**

---

## 🏰 The Three Wings

```
┌─────────────────────────────────────────────────────────────────┐
│                    THE MANSION'S WINGS                          │
├─────────────────┬─────────────────┬─────────────────────────────┤
│   ZEPHYR WING   │   MIMI WING     │   BLUESHIFT WING            │
│                 │                 │                             │
│  • Specialized  │  • Janitor      │  • Specialized              │
│  • Hackathon    │  • Memory       │  • Solana Dev               │
│  • Projects     │  • Archives     │  • Learning                 │
│                 │                 │                             │
└─────────────────┴─────────────────┴─────────────────────────────┘
```

**Wings = Project-specific channels where task agents do the work.**

---

## 💰 Cost Architecture

| Component | API | Cost | Role |
|-----------|-----|------|------|
| **Clawdi** | Moonshot Kimi | $0.50-2/M | Orchestration |
| **Butler** | Moonshot Kimi | $0.50-2/M | Budget mgmt |
| **Gatekeeper** | Moonshot Kimi | $0.50-2/M | Security |
| **Moderator** | Moonshot Kimi | $0.50-2/M | Coordination |
| **Janitor** | Moonshot Kimi | $0.50-2/M | Maintenance |
| **Task Subagents** | NVIDIA/GROQ/OpenRouter | $0 | Specific work |

**Daily Cost:** $0-2 (vs $50-100 with wrong architecture)

---

## 🎯 The Architecture in One Line

> *"Cheap model runs the mansion. Expensive models get hired for shifts. The Butler manages the payroll. The Gatekeeper locks the doors."*

That's how you scale to 15+ parallel agents without scaling costs.

---

## 💡 The Core Principle

If you're running agents and burning through API credits, you don't have a budget problem.

**You have an architecture problem.**

Build the mansion.  
Hire the Butler.  
Let the Patriarch delegate.

---

## 📁 Repository Structure

```
clawdi-infrastructure/
├── README.md              # This architecture document
├── docs/
│   ├── ARCHITECTURE.md    # Detailed system design
│   ├── BUTLER.md          # Token management spec
│   ├── GATEKEEPER.md      # Security protocols
│   └── MODERATOR.md       # Coordination workflows
├── scripts/
│   ├── agent-recovery.sh  # Post-restart recovery
│   └── cost-monitor.py    # Token usage tracking
└── config/
    └── openclaw.json      # Gateway configuration
```

---

## 🚀 Quick Start

1. **Clone and configure**
2. **Setup API keys** (10+ keys across 6 providers)
3. **Configure Butler** priority ledger
4. **Enable Gatekeeper** pre-commit hooks
5. **Start the mansion**

See [docs/SETUP.md](docs/SETUP.md) for detailed instructions.

---

## 📊 Performance Metrics

| Metric | Target | Status |
|--------|--------|--------|
| Agents | 15+ parallel | ✅ Ready |
| Daily Cost | $0-2 | ✅ Configured |
| Token Efficiency | 80%+ free tier | ✅ Active |
| Security | 100% scanned | ✅ Gatekeeper |
| Uptime | 99.9% | 🟡 Monitoring |

---

## 🤝 Contributing

This architecture is battle-tested and production-ready. Contributions welcome:

- Additional provider integrations
- Cost optimization strategies
- Security enhancements
- Documentation improvements

---

## 📜 License

MIT — Build your own mansion.

---

*Built with 💰 efficiency and 🛡️ security in mind.*  
*The future of AI agent orchestration is here.*
