# Evolution Skill for OpenClaw

🧬 Distill reusable patterns from your workspace history into new skills.

<!-- Language Toggle -->
<p align="center">
  <button onclick="toggleLang()" id="lang-btn" style="padding: 8px 16px; cursor: pointer;">中文</button>
</p>

<!-- English Content (default) -->
<div id="en-content">

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

### Method 1: ClawHub

```bash
openclaw skills install evolution
```

### Method 2: Manual

```bash
# Clone or download to your skills directory
cp -r evolution ~/.openclaw/skills/
openclaw gateway restart
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
1. Scan all configured workspaces
2. Identify patterns across 4 categories:
   - 工作边界类 (Cross-workspace boundary issues)
   - 经验复用类 (Reusable prompt templates, workflows)
   - 效率提升类 (Repetitive operations)
   - 系统健康类 (Memory bloat, stale knowledge)
3. Apply quality filters (frequency ≥3, reward, stability)
4. Generate distillation report with candidates
5. You review and confirm each candidate
6. On confirm → generate staging SKILL.md
7. You install manually (strict confirmation)
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

Each skill candidate must pass:
- **Frequency**: Pattern appears ≥3 times
- **Reward**: Measurable time savings
- **Stability**: Not a one-time pattern
- **Boundary**: Doesn't duplicate existing skills
- **Verifiable**: Can design a simple test

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
├── LICENSE               # MIT License
├── config.yaml           # User configuration
├── BASELINE.md           # Incremental scan baseline
├── LAST_RUN.md           # Last run timestamp
├── staging/              # Candidate skills (before install)
└── reports/              # Historical reports
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

</div>

<!-- Chinese Content (hidden by default) -->
<div id="zh-content" style="display: none;">

## 这是什么？

**Evolution** 是一个 OpenClaw Skill，用于扫描你的所有工作区，从历史记录中识别可复用的模式，并生成新的 Skill 候选供你审核——全程由人类掌控。

## 为什么存在？

随着时间推移，你会开发出工作流、修复 bug、发现可以自动化的模式。但这些经验教训都在聊天历史中丢失了。**Evolution** 监控所有工作区中的模式，当发现值得自动化的事情时，提出新的 Skill 建议。

示例：你注意到 researcher agent 经常把文档创建在错误的位置。Evolution 检测到这个模式，提出一个 `smart-archive` Skill 来自动将文档路由到正确的工作区。

## 系统要求

- OpenClaw v2026.4+ (Skill 系统支持)
- 多个工作区（主 agent + subagent）— 有 2+ 个 agent 时效果最佳
- 不需要其他 Skills — 完全独立运行

## 安装方式

### 方式一：ClawHub

```bash
openclaw skills install evolution
```

### 方式二：手动安装

```bash
# 克隆或下载到 skills 目录
cp -r evolution ~/.openclaw/skills/
openclaw gateway restart
```

## 使用方法

| 命令 | 说明 |
|------|------|
| `/evolution` | 运行扫描（增量模式） |
| `/evolution --full` | 全量扫描（忽略上次记录） |
| `/evolution --config` | 编辑配置文件 |
| `/evolution --setup-cron` | 设置定时运行 |
| `/evolution --help` | 显示帮助 |

## 首次运行

首次运行时会询问：

1. **是否扫描过去90天建立基线？** — 建议：是
2. **是否设置定时运行cron？** — 建议：是（每两周一次）

## 工作流程

```
1. 扫描所有已配置的工作区
2. 识别四类模式：
   - 工作边界类（跨工作区文档位置错误）
   - 经验复用类（成功的 Prompt 模板、工作流）
   - 效率提升类（可封装的重复操作）
   - 系统健康类（Memory 膨胀、知识陈旧）
3. 应用质量过滤器
4. 生成蒸馏报告（含 Skill 候选）
5. 你逐个审核并确认
6. 确认后生成正式草稿
7. 你手动安装（严格确认）
```

## 配置

编辑 `~/.openclaw/skills/evolution/config.yaml`：

```yaml
# 排除的工作区
exclude_workspaces:
  - workspace-incognito

# 默认扫描时间跨度（天）
scan_span_days: 30

# 基线保留期（天）
baseline_retention_days: 90

# Cron 计划（留空则禁用）
cron_schedule: ""

# 最大候选数量
max_candidates: 5

# 最小频率阈值
min_frequency: 3
```

## 与周汇报集成

如果你有周汇报（Weekly Summary）Skill，Evolution 会自动集成。

**如果有周汇报：**
- 周汇报中会自动显示 Evolution 运行状态

**如果没有周汇报：**
- 完全独立运行
- 或手动设置 cron：`/evolution --setup-cron`

## 质量门槛

每个 Skill 候选必须通过：
- **频率**：扫描周期内出现≥3次
- **收益**：可量化的节省时间
- **稳定性**：非一次性模式
- **边界**：不与现有 Skill 重叠
- **可验证**：可设计简单验证方式

## 安全声明

Evolution **绝不**：
- 未经确认自动创建 Skill
- 自动安装 Skill
- 修改系统配置
- 删除或移动文件

每个操作都需要你的明确确认。

## 文件结构

```
evolution/
├── SKILL.md              # 主 Skill 文件
├── evolution-guide.md    # 详细工作流程文档
├── README.md             # 本文件
├── LICENSE               # MIT 协议
├── config.yaml           # 用户配置
├── BASELINE.md           # 增量扫描基线
├── LAST_RUN.md           # 上次运行时间戳
├── staging/              # 候选草稿目录
└── reports/              # 历史报告目录
```

## 已知限制

- 模式识别基于启发式（不是 AI 完美的）
- 需要≥3次出现才会提出候选
- 增量扫描需要首次运行建立基线
- 扫描范围不包括 logs/、credentials/、sessions/

## 开源协议

MIT License — 参见 [LICENSE](LICENSE)

## 贡献

欢迎提交 Issue 和 Pull Request。

</div>

<script>
function toggleLang() {
  var en = document.getElementById('en-content');
  var zh = document.getElementById('zh-content');
  var btn = document.getElementById('lang-btn');
  
  if (en.style.display === 'none') {
    en.style.display = 'block';
    zh.style.display = 'none';
    btn.textContent = '中文';
  } else {
    en.style.display = 'none';
    zh.style.display = 'block';
    btn.textContent = 'English';
  }
}
</script>
