# Evolution Skill - Detailed Workflow Guide

This document describes the complete workflow of the evolution skill.

---

## Phase 0: Trigger

**Manual trigger**: `/evolution`

**Weekly summary integration**: If you have a weekly summary skill (周汇报) configured, it will check evolution's last run timestamp. If evolution hasn't run this week, the weekly summary will prompt you:

```
## 进化状态
- 上次运行：从未运行
- 本周状态：⚠️ 未运行
- 推荐：运行 /evolution 进行一次工作区扫描
```

---

## Phase 1: First Run vs Subsequent Runs

### First Run Detection

Evolution detects first run by checking if `~/.openclaw/skills/evolution/BASELINE.md` exists.

**On first run:**
1. Prompt: "首次运行建议扫描更长周期以建立基线。是否扫描过去90天？"
   - Default: Yes (establish comprehensive baseline)
   - User can configure: `baseline_retention_days` in config.yaml
2. Scan configured time span (90 days by default)
3. Record baseline: mtime + size for ALL scanned files in `BASELINE.md`
4. On successful completion: ask once "是否设置定时运行cron？"
5. Create `LAST_RUN.md` with first run timestamp

**After first run:**
1. Load `LAST_RUN.md` → previous scan timestamp
2. Load `BASELINE.md` → baseline mtime + size for each file
3. For each file: if `mtime > last_run OR size ≠ baseline_size` → include in scan
4. Update `BASELINE.md` with new mtime + size
5. Update `LAST_RUN.md`

### Subsequent Run Modes

| Mode | Command | Behavior |
|------|---------|----------|
| Incremental (default) | `/evolution` | Scan only changed files since last run |
| Full | `/evolution --full` | Rescan everything, reset baseline |
| Config | `/evolution --config` | Edit config.yaml |

---

## Phase 2: Workspace Selection

**Default**: All workspaces in `~/.openclaw/`

```
workspace/           (main agent)
workspace-researcher/
workspace-engineer/
workspace-evaluator/
workspace-planner/
workspace-creator/
workspace-incognito/
```

**User-configurable exclude list** in `config.yaml`:

```yaml
exclude_workspaces:
  - workspace-incognito  # Exclude by default (messaging mode)
```

**Workspace scanning priority:**
1. `memory/` - Daily session logs (richest source of patterns)
2. `knowledge/` - Curated reference material
3. `skills/` - Already installed skills (to avoid duplication)
4. Root daily logs (`workspace/*/YYYY-MM-DD.md`)

---

## Phase 3: Scanning

### Scope

```
Included:
├── workspace*/memory/*.md (last N days, default 30)
├── workspace*/knowledge/** (all subdirectories)
├── workspace*/skills/** (installed skills list only)
└── workspace*/SOUL.md, IDENTITY.md, AGENTS.md (for context)

Excluded (security/privacy):
├── logs/
├── credentials/
├── agents/*/sessions/
└── any file with "secret", "password", "token" in path
```

### Incremental Detection Logic

```
For each file in scope:
    stat(file) → mtime, size
    Load BASELINE.md entry for file (if exists)
    
    IF no BASELINE entry:
        ADD to scan (new file)
    ELSE IF mtime > last_run_timestamp:
        ADD to scan (modified)
    ELSE IF size ≠ baseline_size:
        ADD to scan (content changed despite mtime)
    ELSE:
        SKIP (unchanged)
```

### Output

Raw scan data saved to `~/.openclaw/skills/evolution/reports/<date>-raw-scan.md`:

```markdown
# Raw Scan Data - 2026-04-29

## Scan Parameters
- mode: incremental
- scan_span_days: 30
- timestamp: 2026-04-29T18:30:00+08:00

## Files Scanned

| Workspace | File | Size | Modified |
|-----------|------|------|----------|
| workspace | memory/2026-04-28.md | 42618 | 1745932800 |
| workspace-researcher | knowledge/research/... | ... | ... |
```

---

## Phase 4: Pattern Recognition

### Category A: 工作边界类 (Cross-Workspace Issues)

**What to detect:**
- Documents created in one workspace but referenced from another
- Knowledge stored in researcher workspace but needed by main
- Files that should be centralized but are scattered

**Detection signals:**
- "放在workspace-researcher" in memory logs
- Cross-workspace file references
- "无法找到" / "找不到" patterns
- Knowledge silo complaints

**Example candidate:**
```
smart-archive: 自动检查researcher创建的文档是否需要同步到主工作区
```

### Category B: 经验复用类 (Reusable Patterns)

**What to detect:**
- Repeated successful prompt templates
- Consistent workflow steps across sessions
- Debug paths that solved problems

**Detection signals:**
- Similar instructions appearing ≥3 times
- "成功" / "解决了" following consistent steps
- Repeated tool use sequences

**Example candidate:**
```
research-briefing-template: 标准化的行业简报生成模板
```

### Category C: 效率提升类 (Efficiency Gains)

**What to detect:**
- Repeated manual operations
- Commands that could be automated
- Tedious multi-step processes

**Detection signals:**
- "重复" / "每次都" / "每次都要"
- Same bash command executed repeatedly
- Copy-paste patterns in memory logs

**Example candidate:**
```
brand-research-flow: 品牌研究的标准工作流封装
```

### Category D: 系统健康类 (System Health)

**What to detect:**
- Memory files growing excessively
- Knowledge contradictions
- Configuration drift

**Detection signals:**
- Memory file size > 100KB
- "矛盾" / "冲突" in knowledge files
- Frequent re-explaining of same concept

**Example candidate:**
```
memory-maintenance: 定期清理和压缩memory文件的机制
```

---

## Phase 5: Quality Filtering

Each candidate must pass ALL gates:

### Basic Gates (must pass)
| Gate | Threshold | Rationale |
|------|-----------|----------|
| Frequency | ≥3 occurrences in scan period | Not random noise |
| Reward | Time savings OR error reduction identifiable | Worth the effort |
| Stability | Pattern stable across different contexts | Won't break soon |
| Boundary | Does not overlap with existing skills | No duplication |

### Execution Constraint Gate (must pass)
| Gate | Requirement |
|------|-------------|
| Verifiable | Must include a concrete verification method |
| Trigger Clarity | Must specify exact trigger conditions, not vague |
| Actionable | Must define concrete actions |
| Feedback Loop | Must define how skill confirms correct execution |

### OpenClaw Compliance Gate (must pass)
| Gate | Requirement |
|------|-------------|
| Architecture Fit | Follows OpenClaw best practices |
| Tool Alignment | Can be executed with OpenClaw's tool set |
| Context Appropriate | Scope appropriate for a skill |

**Auto-rejection criteria:**
- Frequency < 3 → "too rare"
- No clear reward → "insufficient benefit"
- Only happened once → "one-time issue"
- Overlaps with existing skills → "duplicate functionality"
- Cannot design test → "unverifiable"
- Vague trigger conditions → "需要重构"
- No feedback loop → "需要重构"

---

## Phase 6: Distillation Report

Output to `~/.openclaw/skills/evolution/reports/<date>-report.md`:

```markdown
# 进化报告 - 2026-04-29

## 运行状态
- 上次运行：从未运行
- 本周工作区变动：workspace-researcher (+3), workspace-engineer (+5)

---

## Skill候选推荐

### 候选 #1：smart-archive

**类型**：工作边界类

**问题描述**：
researcher创建的文档经常存放在本工作区而非主工作区，导致知识孤岛。

**Pattern证据**：
| 时间 | 证据摘要 |
|------|----------|
| 2026-04-17 | researcher生成了品牌报告，放在workspace-researcher/knowledge/ |
| 2026-04-20 | feishu wiki文档位置错误，main无法发现 |
| 2026-04-25 | 类似的文档位置错误再次出现 |

**影响评估**：
- 频率：3次/30天 ✅
- 收益：减少手动整理，每年节省约2小时
- 稳定性：高（跨多个研究项目）
- 边界：与现有skill不重叠 ✅
- 可验证：可以设计简单测试 ✅

**建议的Skill结构**：
```
smart-archive/
├── SKILL.md          # 主指令
├── references/      # 操作规范
└── scripts/          # 归档脚本（草稿）
```

**预估工作量**：2-3小时

---

## 你的操作

对于每个候选，请回复：
- **确认** → 我生成正式草稿到 `staging/`
- **待定** → 在下次evolution运行时再考虑，不遗忘
- **拒绝** → 记录拒绝理由（用于未来过滤）
- **修改** → 告诉我需要修改什么

[候选 #1] 确认/待定/拒绝/修改？
[候选 #2] ...
```

---

## Phase 7: User Confirmation + Installation

**Step 1: Confirm candidate**

User replies "确认" for a candidate.

**Step 2: Generate staging SKILL.md**

Evolution generates full SKILL.md in `staging/<candidate-name>/`.

**Step 3: Prompt installation**

```
已生成 smart-archive 草稿于 ~/.openclaw/skills/evolution/staging/smart-archive/

是否帮你安装？
- 输入"是"：我将提供安装命令（你手动执行）
- 输入"否"：草稿保留，随时可以手动安装
```

**Step 4: Strict confirmation for installation**

```
⚠️ 安装确认
Skill: smart-archive
位置: ~/.openclaw/skills/evolution/staging/smart-archive/
操作: 复制到 ~/.openclaw/skills/smart-archive/

此操作将：
- 在 ~/.openclaw/skills/ 下创建 smart-archive/
- 安装后需要重启网关: openclaw gateway restart

确认安装？ (yes/no)
```

**Step 5: Provide install command (NOT auto-install)**

```
安装命令：
cp -r ~/.openclaw/skills/evolution/staging/smart-archive ~/.openclaw/skills/
openclaw gateway restart

复制后告诉我完成，我会验证安装是否成功。
```

---

## Phase 8: Installed Skill Tracking

**Purpose**: Track evolution-generated skills and generate optimization candidates.

### Registry File: evolution-installed.md

When user confirms installation of an evolution-generated skill:

```
Evolution asks: "Install complete?"
If yes → record to ~/.openclaw/skills/evolution/evolution-installed.md
```

### Tracking During Scan

During Phase 1 scan, for each active skill in `evolution-installed.md`:
1. Search recent memory for skill references and trigger keywords
2. Check if constraint was followed or bypassed
3. Log trigger count and effectiveness
4. Generate optimization candidates for low-effectiveness skills

### Optimization Trigger Conditions

| Condition | Action |
|-----------|--------|
| Skill rarely triggered | Mark as low value, consider removing |
| Skill triggered but ineffective | Generate optimization candidate |
| OpenClaw architecture changed | Mark as potentially outdated |

### Output in Distillation Report

```markdown
## 已安装Skill优化候选

### authorization-norms-ref (需要优化)
- 触发次数：1次
- 有效性：低
- 问题：规则存在但无执行机制
- 建议：重构为P0-core hook集成或移除
- 操作：[优化/移除/保留]
```

---

## Phase 9: Memory Log Generation

**Purpose**: Create a memory log after each evolution run for continuity.

### When Generated

After Phase 7 (Manual Install) completes, before ending the evolution session.

### Output Location

`~/.openclaw/skills/evolution/memory/YYYY-MM-DD.md`

### Format

```markdown
# Evolution Memory Log - 2026-04-29

## Run Summary
- mode: incremental
- scan_span_days: 30
- files_scanned: 47
- candidates_found: 2
- candidates_confirmed: 1
- candidates_deferred: 0
- candidates_rejected: 1

## Installed Skill Tracking
| Skill | Status | Last Triggered | Effectiveness |
|-------|--------|----------------|----------------|
| authorization-norms-ref | needs_optimization | 2026-04-29 | low |

## Notes
- First run with 90-day baseline
- Generated candidates: authorization-norms-ref, steve-commands-regression-check
- authorization-norms-ref installed, flagged for optimization
```

---

## Phase 10: Weekly Summary Integration

The weekly summary skill (周汇报) should check evolution status:

```markdown
## 进化状态
- 上次运行：2026-04-22（3天前）
- 本周运行状态：✅ 已运行

- 上次运行：从未运行
- 本周运行状态：⚠️ 未运行
- 推荐：运行 /evolution 进行一次工作区扫描
```

**Implementation in weekly summary skill:**

```markdown
## 进化状态
- 上次运行：{{ evolution.last_run }}
- 本周状态：{{ if evolution.run_this_week then "✅ 已运行" else "⚠️ 未运行" }}
{{ if not evolution.run_this_week }}
- 推荐：运行 /evolution 进行一次工作区扫描
{{ end }}
```

---

## Strictly Prohibited Actions

| Action | Forbidden Because |
|--------|-------------------|
| Auto-creating skills | User must approve each |
| Auto-installing skills | Irreversible permission grant |
| Modifying system configs | Candidates may contain errors |
| Deleting/moving files | Only recommendations |
| Modifying other skills | Boundary pollution |

---

## File Structure

```
~/.openclaw/skills/evolution/
├── SKILL.md                    # Main skill instructions
├── evolution-guide.md          # This workflow doc
├── config.yaml                 # User preferences
├── config.yaml.example        # Config template
├── BASELINE.md                 # Baseline mtime+size for incremental scan
├── LAST_RUN.md                 # Timestamp of last run
├── evolution-installed.md      # Registry of evolution-generated installed skills
├── staging/                    # Confirmed candidates get generated here
│   └── .gitkeep
├── reports/                    # Historical distillation reports
│   └── 2026-04/
│       └── 2026-04-29-report.md
└── memory/                     # Evolution's own memory (runtime generated)
    └── 2026-04-29.md
```
