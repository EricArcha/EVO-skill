---
name: evolution
slug: evolution
version: 1.1.0
homepage: https://github.com/EricArcha/EVO-skill
changelog: "Add defer option, execution constraints, installed skill optimization, and OpenClaw compliance gates"
description: "EN: Distill reusable patterns from workspace history into new skills with human-in-the-loop approval.\nZH: 从工作区历史中提炼可复用模式，生成新的 Skills，全程由你掌控。"
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

Each skill candidate must pass ALL gates:

### Basic Gates (must pass)
| Gate | Requirement |
|------|-------------|
| **Frequency** | Pattern appears ≥3 times in scan period |
| **Reward** | Measurable time savings or error reduction |
| **Stability** | Not a one-time/adhoc pattern |
| **Boundary** | Doesn't overlap with existing skills |

### Execution Constraint Gate (must pass)
| Gate | Requirement |
|------|-------------|
| **Verifiable** | Must include a concrete verification method — how do we test this skill actually works? |
| **Trigger Clarity** | Must specify exact trigger conditions, not vague "remind me to..." |
| **Actionable** | Must define concrete actions, not just descriptions |
| **Feedback Loop** | Must define how the skill confirms it executed correctly |

### OpenClaw Compliance Gate (must pass)
| Gate | Requirement |
|------|-------------|
| **Architecture Fit** | Does this follow OpenClaw best practices? (e.g., knowledge centralized, rules in proper locations) |
| **Tool Alignment** | Can this actually be executed with OpenClaw's tool set? |
| **Context Appropriate** | Is the scope appropriate for a skill, not too broad or too narrow? |

**If a candidate fails Execution Constraint Gate or OpenClaw Compliance Gate:**
- Mark as "需要重构" (needs refactoring)
- Provide specific feedback on what must change
- Do not generate staging SKILL.md until refactored

## User Response Options

For each candidate, reply:
- **确认 (confirm)** → Generate formal SKILL.md in staging/
- **待定 (defer)** → Include in next evolution run, don't forget
- **拒绝 (reject)** → Record rejection reason (for future filtering)
- **修改 (modify)** → Tell me what needs to change

## Phase 8: Installed Skill Tracking

For skills that evolution generated and you installed:

1. **Track execution**: Note when the skill is triggered and its effectiveness
2. **Flag issues**: If the skill fails to constrain behavior, generate an optimization candidate
3. **Report**: Include installed skill performance in next evolution report

**Optimization trigger conditions:**
- Skill rarely triggered → low value, consider removing
- Skill triggered but ineffective → needs refactoring
- OpenClaw architecture changed → skill may be outdated

## Fallback for Non-周汇报 Users

If you don't have a weekly summary skill:

1. **Manual mode**: Run `/evolution` whenever you want
2. **Cron mode**: Set up `/evolution --setup-cron` for bi-weekly runs
3. **Flexible**: Works completely standalone
