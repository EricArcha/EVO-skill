# Evolution Skill for OpenClaw

🧬 从工作区历史中提炼可复用的模式，生成新的 Skills。

## 简介

**Evolution** 是一个 OpenClaw Skill，用于扫描你的所有工作区，从历史记录中识别可复用的模式，并生成新的 Skill 候选供你审核——全程由人类掌控。

## 解决的问题

- 某个 Session 中学到的经验，下一个 Session 就忘了
- 可以自动化的工作流，每次都要手动重复
- Sub-agent 创建的文档经常放错位置，形成知识孤岛

## 功能特点

| 特点 | 说明 |
|------|------|
| **人类全程掌控** | 每个候选都需要用户确认，绝不自动创建 |
| **增量扫描** | 首次运行建立基线，后续只扫描增量变化 |
| **四维模式识别** | 工作边界、经验复用、效率提升、系统健康 |
| **质量门槛** | 频率≥3 + 收益+稳定性+边界+可验证 |
| **零自动执行** | 不自动安装、不修改系统配置、不删除文件 |

## 安装方式

### 方式一：ClawHub

```bash
openclaw skills install evolution
```

### 方式二：手动安装

```bash
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

1. **是否扫描过去90天建立基线？** — 建议：是（建立完整基线）
2. **是否设置定时运行cron？** — 建议：每两周一次

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

编辑 `config.yaml`：

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
```

## 与周汇报集成

如果你有周汇报（Weekly Summary）Skill，Evolution 会自动集成。

**如果有周汇报：**
- 周汇报中会自动显示 Evolution 运行状态
- 如果本周未运行，会提示你运行

**如果没有周汇报：**
- 完全独立运行
- 或手动设置 cron：`/evolution --setup-cron`

## 文件结构

```
evolution/
├── SKILL.md              # 主 Skill 文件
├── evolution-guide.md    # 详细工作流程文档
├── README.md             # 本文件
├── README-zh.md          # 中文版（本文）
├── LICENSE               # MIT 协议
├── config.yaml           # 用户配置
├── BASELINE.md           # 增量扫描基线
├── LAST_RUN.md           # 上次运行时间戳
├── staging/              # 候选草稿目录
└── reports/              # 历史报告目录
```

## 质量门槛

每个 Skill 候选必须通过：

| 检验项 | 说明 |
|--------|------|
| **频率** | 扫描周期内出现≥3次 |
| **收益** | 可量化的节省时间或减少错误 |
| **稳定性** | 非一次性/临时性模式 |
| **边界** | 不与现有 Skill 重叠 |
| **可验证** | 可设计简单验证方式 |

## 安全声明

Evolution **绝不**：
- 未经确认自动创建 Skill
- 自动安装 Skill
- 修改系统配置
- 删除或移动文件
- 修改其他 Skill

每个操作都需要你的明确确认。

## 开源协议

MIT License

## 贡献

欢迎提交 Issue 和 Pull Request。
