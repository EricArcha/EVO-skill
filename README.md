# Evolution Skill for OpenClaw

🧬 Distill reusable patterns from your workspace history into new skills.

[中文版](README_zh.md) | English

## What is this?

**Evolution** is an OpenClaw skill that scans your workspaces, identifies reusable patterns from your work history, and generates new skill candidates for your review — with full human-in-the-loop control.

## Why does it exist?

Over time, you develop workflows, fix bugs, and discover patterns that could be automated. But these insights are lost in chat history. **Evolution** watches for patterns across all your workspaces and proposes new skills when it finds something worth automating.

Example: You notice that your researcher agent keeps creating documents in the wrong location. Evolution detects this pattern and proposes a `smart-archive` skill to automatically route documents to the correct workspace.

## Requirements

- OpenClaw v2026.4+ (for skill system support)
- Multiple workspaces (main + subagents) — works best with 2+ agents
- No other skills required — fully standalone

## Installation

### Method 1: GitHub (Recommended)

```bash
# Clone directly to your skills directory
git clone https://github.com/EricArcha/EVO-skill.git ~/.openclaw/skills/evolution
openclaw gateway restart
```

### Method 2: Manual

Download or clone this repository to your skills directory:

```bash
cp -r evolution ~/.openclaw/skills/
openclaw gateway restart
```

### Note on ClawHub

ClawHub publication pending. Once published, you will be able to install via:
```bash
openclaw skills install evolution
```

## Usage

| Command | Description |
|---------|-------------|
| `/evolution` | Run scan (incremental mode) |
| `/evolution --full` | Full rescan (ignores last run) |
| `/evolution --config` | Edit configuration |
| `/evolution --setup-cron` | Interactive cron setup |
| `/evolution --help` | Show help |

## First Run

On first run, Evolution will ask:

1. **"是否扫描过去90天建立基线？"** — Scan last 90 days to establish baseline?
2. **"是否设置定时运行cron？"** — Set up automatic runs?

Both are recommended for best results.

## Workflow

```
Phase 1: Scan
- Scan configured workspaces (memory/, knowledge/, skills/)

Phase 2: Pattern Recognition
- Identify patterns across 4 categories:
  - 工作边界类 (Cross-workspace boundary issues)
  - 经验复用类 (Reusable prompt templates, workflows)
  - 效率提升类 (Repetitive operations that can be automated)
  - 系统健康类 (Memory bloat, stale knowledge)

Phase 3: Quality Filtering
- Apply frequency, reward, stability, boundary, and OpenClaw compliance gates

Phase 4: Generate Report
- Generate distillation report with candidate skills

Phase 5: User Review
- You review and confirm each candidate (Confirm/Defer/Reject/Modify)

Phase 6: Generate Staging
- On confirmation, skill generates staging SKILL.md

Phase 7: Manual Install
- You install manually (strict confirmation each time)

Phase 8: Track Installed Skills
- Track evolution-generated skills for optimization opportunities
```

## Configuration

Edit `~/.openclaw/skills/evolution/config.yaml`:

```yaml
# Workspaces to exclude
exclude_workspaces:
  - workspace-incognito

# Scan time span (days)
scan_span_days: 30

# Baseline retention (days)
baseline_retention_days: 90

# Cron schedule (leave empty to disable)
cron_schedule: ""

# Max candidates per run
max_candidates: 5

# Min frequency threshold
min_frequency: 3
```

## Integration with Weekly Summary

Evolution integrates with weekly summary skills (周汇报) if you have one configured.

**If you have weekly summary:**
- Evolution status appears automatically in your weekly report

**If you don't have weekly summary:**
- Works completely standalone
- Or set up a cron: `/evolution --setup-cron`

## Quality Gates

Each skill candidate must pass ALL gates:

### Basic Gates (must pass)
- **Frequency**: Pattern appears ≥3 times
- **Reward**: Measurable time savings
- **Stability**: Not a one-time pattern
- **Boundary**: Doesn't duplicate existing skills

### Execution Constraint Gate (must pass)
- **Verifiable**: Must include a concrete verification method
- **Trigger Clarity**: Must specify exact trigger conditions
- **Actionable**: Must define concrete actions
- **Feedback Loop**: Must define how the skill confirms it executed correctly

### OpenClaw Compliance Gate (must pass)
- **Architecture Fit**: Follows OpenClaw best practices
- **Tool Alignment**: Can be executed with OpenClaw's tool set
- **Context Appropriate**: Scope appropriate for a skill

## User Response Options

For each candidate, reply:
- **确认 (confirm)** → Generate formal SKILL.md in staging/
- **待定 (defer)** → Include in next evolution run, don't forget
- **拒绝 (reject)** → Record rejection reason (for future filtering)
- **修改 (modify)** → Tell me what needs to change

## Strictly No Auto-Execution

Evolution NEVER:
- Auto-creates skills without confirmation
- Auto-installs skills
- Modifies system configuration
- Deletes or moves files

Every action requires your explicit confirmation.

## File Structure

```
evolution/
├── SKILL.md              # Main skill instructions
├── evolution-guide.md    # Detailed workflow doc
├── README.md             # This file
├── README_zh.md          # Chinese version
├── LICENSE               # MIT License
├── config.yaml           # User configuration
├── config.yaml.example  # Config template
├── BASELINE.md           # Incremental scan baseline
├── LAST_RUN.md           # Last run timestamp
├── evolution-installed.md # Registry of evolution-generated installed skills
├── staging/              # Candidate skills (before install)
├── reports/              # Historical reports
└── memory/               # Evolution's own memory (runtime generated)
```

## Known Limitations

- Pattern recognition is heuristic-based (not AI-perfect)
- Requires ≥3 occurrences to propose a candidate
- Incremental scan requires first run to establish baseline
- Scan scope excludes logs/, credentials/, sessions/

## License

MIT License — see [LICENSE](LICENSE)

## Contributing

Issues and pull requests welcome. Please read the evolution-guide.md for workflow details.
