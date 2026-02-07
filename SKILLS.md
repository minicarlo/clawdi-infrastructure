# Skills Documentation

Overview of AgentSkills used in the Clawdi infrastructure.

## What are Skills?

Skills are modular packages that extend OpenClaw agent capabilities. They provide:
- **Specialized workflows** - Multi-step procedures for specific domains
- **Tool integrations** - Instructions for working with APIs, CLIs, file formats
- **Domain expertise** - Procedural knowledge that models don't possess
- **Bundled resources** - Scripts, references, and assets

## Installed Skills

### butler

**Location:** `~/.openclaw/workspace/skills/butler/`  
**Used by:** butler-agent  
**Purpose:** API budget tracking & model recommendations

**Files:**
```
butler/
├── SKILL.md              # Main documentation
├── scripts/
│   ├── check_budgets.py     # Budget status across all providers
│   ├── recommend_model.py   # Task→model assignment logic
│   ├── log_usage.py         # Manual usage tracking
│   └── rotate_key.py        # Key rotation (Phase 2)
└── references/
    └── API_KEYS.md          # Provider details & rate limits
```

**Key Features:**
- Tracks 6 providers: Anthropic, Groq, Nvidia, OpenAI, OpenRouter, OpenCode
- Complexity-based model recommendations (simple→free, complex→premium)
- Real-time budget monitoring
- Alert thresholds: 🟢 0-50%, 🟡 50-80%, 🔴 80-95%, ⚠️ 95%+

**Example:**
```bash
python3 scripts/recommend_model.py "create a landing page"
# Output: groq/llama-3.3-70b-versatile (FREE)

python3 scripts/check_budgets.py
# Output: Budget breakdown by provider
```

---

### janitor

**Location:** `~/.openclaw/workspace/skills/janitor/`  
**Used by:** janitor-agent  
**Purpose:** GitHub repository cleanup automation

**Files:**
```
janitor/
├── SKILL.md                # Main documentation
├── scripts/
│   ├── scan_repo.py           # Identify cleanup targets
│   ├── cleanup_files.py       # Remove junk safely
│   └── optimize_repo.py       # Full cleanup workflow
└── references/
    └── JUNK_PATTERNS.md       # Common junk file patterns
```

**Cleanup Categories:**
- **dependencies** - node_modules, vendor, .yarn
- **build_artifacts** - dist, build, *.pyc, __pycache__
- **cache** - .cache, .parcel-cache, *.log
- **temp** - tmp, *.tmp, .DS_Store, Thumbs.db
- **ide** - .vscode, .idea, *.sublime-*
- **test_coverage** - coverage, .nyc_output

**Example:**
```bash
# Scan repo
python3 scripts/scan_repo.py minicarlo/repo-name

# Dry run
python3 scripts/optimize_repo.py minicarlo/repo-name --dry-run

# Full optimization
python3 scripts/optimize_repo.py minicarlo/repo-name
```

**Safety Features:**
- Dry-run mode (preview before deletion)
- Auto-updates .gitignore
- JSON audit logs
- Branch protection (never deletes main/master)

---

### github

**Location:** `/root/.openclaw/workspace/skills/github/`  
**Used by:** janitor-agent, gatekeeper-agent  
**Purpose:** GitHub operations via `gh` CLI

**Capabilities:**
- Create/manage issues and PRs
- CI/CD workflow management
- Repository operations
- Branch management
- Advanced queries via GraphQL

**Common Operations:**
```bash
# Issue management
gh issue create --title "Bug" --body "Description"
gh issue list --state open

# PR management
gh pr create --title "Feature" --body "Changes"
gh pr list --state open

# Repository operations
gh repo view owner/repo
gh repo clone owner/repo

# CI/CD
gh run list
gh run view <run-id>
```

---

### github-secrets-manager

**Location:** `~/.openclaw/workspace/skills/github-secrets-manager/`  
**Used by:** Main agent (Clawdi)  
**Purpose:** Secure API key storage in GitHub repository secrets

**Operations:**
```bash
# Store secret
gh secret set SECRET_NAME --body "value" --repo owner/repo

# List secrets (values hidden)
gh secret list --repo owner/repo

# Update secret (overwrites)
gh secret set SECRET_NAME --body "new-value" --repo owner/repo

# Delete secret
gh secret delete SECRET_NAME --repo owner/repo
```

**Security Notes:**
- Secrets are encrypted by GitHub at rest
- Values cannot be retrieved after storage (security feature)
- Use standard naming conventions (e.g., ANTHROPIC_API_KEY)

**Stored Secrets:**
- OPENCODE_API_KEY
- TELEGRAM_BOT_TOKEN
- (Others as needed)

---

### skill-creator

**Location:** `/usr/lib/node_modules/openclaw/skills/skill-creator/`  
**Used by:** Main agent (Clawdi)  
**Purpose:** Create and package new AgentSkills

**Workflow:**
1. **Understand the skill** - Concrete examples of usage
2. **Plan resources** - Scripts, references, assets needed
3. **Initialize** - Run `init_skill.py`
4. **Edit** - Implement SKILL.md and resources
5. **Package** - Run `package_skill.py` to create `.skill` file
6. **Iterate** - Test and refine

**Example:**
```bash
# Initialize new skill
python3 scripts/init_skill.py my-skill --path /skills --resources scripts,references

# Package skill
python3 scripts/package_skill.py /skills/my-skill
# Output: my-skill.skill (distributable package)
```

**Skill Anatomy:**
```
my-skill/
├── SKILL.md (required)
│   ├── YAML frontmatter (name, description)
│   └── Markdown instructions
├── scripts/ (optional)
│   └── *.py, *.sh - Executable code
├── references/ (optional)
│   └── *.md - Documentation to load as needed
└── assets/ (optional)
    └── * - Files used in output (templates, icons, etc.)
```

---

## Skill Development Patterns

### When to Create a New Skill

✅ **Create a skill when:**
- Task requires multi-step procedural knowledge
- Working with specialized file formats/APIs
- Code is being rewritten repeatedly
- Domain-specific expertise needed
- Bundled assets/templates would help

❌ **Don't create a skill when:**
- Single-shot command is sufficient
- Model already has the knowledge
- Task is too variable/context-dependent

### Progressive Disclosure

Keep SKILL.md lean (<500 lines). Use references for:
- Detailed API documentation
- Large schemas or examples
- Domain-specific guides
- Multiple framework variants

**Pattern:**
```markdown
# SKILL.md
Quick start guide and core workflows.

For details see:
- [API Reference](references/api.md)
- [Examples](references/examples.md)
```

---

## Skill Management

### Installing Skills

**From ClawHub:**
```bash
clawhub install skill-name
```

**From local directory:**
```bash
cp -r /path/to/skill ~/.openclaw/workspace/skills/
```

**From .skill package:**
```bash
unzip skill-name.skill -d ~/.openclaw/workspace/skills/
```

### Updating Skills

**Pull latest from ClawHub:**
```bash
clawhub update skill-name
```

**Manual update:**
- Edit files in `~/.openclaw/workspace/skills/skill-name/`
- No restart needed (skills loaded on demand)

### Listing Skills

```bash
ls ~/.openclaw/workspace/skills/
# Output: butler, janitor, github, github-secrets-manager, skill-creator
```

---

## Custom Skills for This Setup

### butler

**Custom logic:**
- Multi-provider tracking (6 providers)
- Complexity assessment algorithm
- Cost-optimized recommendations
- Free-model preference

**Future enhancements:**
- Automated key rotation
- Real-time rate limit monitoring
- Predictive cost forecasting
- Provider health checks

### janitor

**Custom logic:**
- GitHub-specific cleanup patterns
- Safe deletion with dry-run
- Auto-gitignore updates
- Branch cleanup integration

**Future enhancements:**
- Multi-repo batch operations
- Scheduled cleanups via cron
- Duplicate detection improvements
- Size trend tracking

---

## Skill Contribution

Want to share a skill?
1. Package it: `package_skill.py skill-folder`
2. Publish to ClawHub: `clawhub publish skill-name.skill`
3. Document usage and examples
4. Share in Discord community

---

## Resources

- [OpenClaw Skills Documentation](https://docs.openclaw.ai/skills)
- [ClawHub Skill Repository](https://clawhub.com)
- [Skill Creator Guide](https://docs.openclaw.ai/skills/creating)
