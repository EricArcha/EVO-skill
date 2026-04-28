# Evolution Skill for OpenClaw

🧬 从工作区历史中提炼可复用的模式，生成新的 Skills。

[English](README.md) | 中文版

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

### 方式一：GitHub（推荐）

```bash
# 直接克隆到 skills 目录
git clone https://github.com/EricArcha/EVO-skill.git ~/.openclaw/skills/evolution
openclaw gateway restart
```

### 方式二：手动安装

下载或克隆本仓库到 skills 目录：

```bash
cp -r evolution ~/.openclaw/skills/
openclaw gateway restart
```

### ClawHub 安装（待发布）

ClawHub 发布后可通过以下命令安装：
```bash
openclaw skills install evolution
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
├── README.md             # 英文版
├── README_zh.md         # 本文件（中文版）
├── LICENSE              # MIT 协议
├── config.yaml           # 用户配置
├── BASELINE.md          # 增量扫描基线
├── LAST_RUN.md          # 上次运行时间戳
├── staging/             # 候选草稿目录
├── reports/            # 历史报告目录
└── memory/              # Evolution 自身记忆（运行时生成）
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
