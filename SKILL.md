---
name: evolution
slug: evolution
version: 1.0.0
homepage: https://github.com/your-repo/evolution-skill
changelog: "Initial release"
description: Distill reusable patterns from workspace history into new skills with human-in-the-loop approval. Scans workspaces, identifies patterns, and generates skill candidates for user review.
metadata: {"clawdbot":{"emoji":"🧬","requires":{"bins":[]},"os":["linux","darwin","win32"]}}
---

# Evolution Skill

Scans all OpenClaw workspaces, distills reusable patterns, and generates skill candidates for your review.

## When to Use

Run when you want to:
- Identify reusable patterns from recent workspace activity
- Generate new skills based on demonstrated workflows
- Find cross-workspace issues (docs in wrong location, knowledge silos)
- Improve automation based on past successes

## Usage

| Command | Description |
|---------|-------------|
| `/evolution` | Run evolution scan (incremental mode, last 30 days) |
| `/evolution --full` | Full rescan (ignores last run, rescans everything) |
| `/evolution --config` | Edit evolution configuration |
| `/evolution --setup-cron` | Interactive cron setup |
| `/evolution --help` | Show help |

## First Run

On first run, you will be prompted:
1. "是否扫描过去90天建立基线？" — Scan last 90 days to establish baseline?
2. "是否设置定时运行cron？" — Set up automatic runs?

## Workflow

```
1. Scan configured workspaces (memory/, knowledge/, skills/)
2. Identify patterns across 4 categories:
   - 工作边界类 (Cross-workspace boundary issues)
   - 经验复用类 (Reusable prompt templates, workflows)
   - 效率提升类 (Repetitive operations that can be automated)
   - 系统健康类 (Memory bloat, stale knowledge)
3. Apply quality filters (frequency ≥3, reward, stability, boundary)
4. Generate distillation report with candidate skills
5. You review and confirm each candidate
6. On confirmation, skill generates staging SKILL.md
7. You install manually (strict confirmation each time)
```

## Output Location

- Reports: `~/.openclaw/skills/evolution/reports/<date>-report.md`
- Staging candidates: `~/.openclaw/skills/evolution/staging/<candidate-name>/`

## Configuration

Edit `~/.openclaw/skills/evolution/config.yaml`:

```yaml
# Workspaces to exclude from scanning
exclude_workspaces:
  - workspace-incognito

# Default scan time span (days)
scan_span_days: 30

# Baseline retention period (days)
baseline_retention_days: 90

# Cron schedule (leave empty to disable)
cron_schedule: ""
```

## Integration with Weekly Summary

This skill integrates with weekly summary (周汇报) if you have it configured.

**If you have weekly summary:**
- Evolution status appears automatically in your weekly report
- You'll be prompted if evolution hasn't run this week

**If you don't have weekly summary:**
- Run `/evolution` manually whenever you want
- Or set up a cron: `/evolution --setup-cron`
- Evolution works standalone without any other skills

## Strictly No Auto-Execution

This skill NEVER:
- Auto-creates skills without confirmation
- Auto-installs skills
- Modifies system configuration
- Deletes or moves files
- Overwrites existing skills

Every action requires your explicit confirmation.

## Quality Gates

Each skill candidate must pass:
- **Frequency**: Pattern appears ≥3 times in scan period
- **Reward**: Measurable time savings or error reduction
- **Stability**: Not a one-time/adhoc pattern
- **Boundary**: Doesn't overlap with existing skills
- **Verifiable**: Can design a simple test for it

## Fallback for Non-周汇报 Users

If you don't have a weekly summary skill:

1. **Manual mode**: Run `/evolution` whenever you want
2. **Cron mode**: Set up `/evolution --setup-cron` for bi-weekly runs
3. **Flexible**: Works completely standalone
