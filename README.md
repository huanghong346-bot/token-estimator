# Token Estimator

面向 vibe coding 用户的 token 消耗与费用预估工具。不追求精确，而是基于经验系数
给出乐观/正常/悲观三档量级范围，帮你做好预算预期。

## 安装

### Claude Code

```bash
# 复制到 Claude Code skills 目录
cp -r token-estimator ~/.claude/skills/token-estimator
```

### Cursor

```bash
cp -r token-estimator ~/.cursor/skills/token-estimator
```

### 其他支持 SKILL.md 的工具

将 `token-estimator/` 目录复制到对应工具的 skills 目录即可。

## 使用方式

### 自动触发

本 Skill 会自动检测以下关键词并触发：

- **中文**：token、费用、成本、预算、预估、估算、消耗、开始新项目
- **英文**：token, cost, budget, estimate, estimate tokens

描述项目想法时也会自动触发。

### 手动触发

在任何对话中输入：

```
/estimate
```

### 快捷命令

| 命令 | 功能 |
|------|------|
| `/estimate` | 触发新预估或更新当前预估 |
| `/estimate-reset` | 清除当前项目，开始新预估 |
| `/estimate-history` | 显示历史项目列表和统计 |
| `/estimate-compare claude-sonnet-4` | 对比切换模型后的费用差异 |
| `/estimate-level` | 显示或手动设置用户等级 |
| `/estimate-config` | 显示当前配置 |

### 项目进行中的更新

预估不是一次性的。在项目进行中，以下情况会触发更新：
- 你主动要求更新
- 需求发生显著变化
- 检测到 CLAUDE.md 等工具有更新

### 项目结束

当项目完成时，说"项目完成"或"/estimate"，工具会引导你收集实际消耗数据，
并自动更新校准系数，让下次预估更准确。

## 数据存储

所有数据保存在 `~/.token-estimator/` 目录下，**仅存储在本地**，不上传任何数据。

```
token-estimator/
├── SKILL.md
├── README.md
├── references/
│   ├── pricing.json              # 模型定价
│   ├── base_tokens.json          # 基础 token 量参考
│   └── model_intelligence.json   # 模型智能度评测数据
├── templates/
│   └── history_template.json     # 历史数据模板
└── calibration/
    └── first_record.json         # 第一组校准数据
```

## 预估模型

### 公式

```
总 token = 基础量 × 用户经验系数 × 上下文膨胀系数 × 工具辅助系数
         × 模型智能度系数 × 提示词完整度系数 × 历史校准系数
```

### 三档预估

| 档位 | 说明 |
|------|------|
| 乐观 | 一切顺利，按最小基础量和最优系数计算 |
| 正常 | 中位基础量和正常系数，最可能的结果 |
| 悲观 | 最大基础量 × 1.3 缓冲区，考虑返工和意外 |

### 检测的效率工具

| 工具 | 系数 | 说明 |
|------|------|------|
| CLAUDE.md | 0.90x | 完善的项目指令文件 |
| Hermes Agent | 0.75x | 多智能体任务管理 |
| everything-claude-code | 0.85x | ECC 增强工具集 |
| .cursorrules | 0.90x | Cursor 项目规则 |
| 自定义 harness | 0.80x | 自定义 hooks 和 agents |

多工具叠乘，下限 0.55x。

### 模型智能度系数

不同模型解决同一问题的效率不同。基于 SWE-bench Verified、LiveCodeBench、Terminal-Bench 三大评测：

| 等级 | 系数 | 代表模型 |
|------|------|---------|
| S级 | 0.80x | Claude Opus 4.6, DeepSeek V4 Pro, GPT-5.4 |
| A级 | 0.90x | Claude Sonnet 4.6, Gemini 3.1 Pro, Kimi K2.5 |
| B级 | 1.00x | DeepSeek V3.2, DeepSeek R1, MiniMax M2.5 |
| C级 | 1.25x | DeepSeek V3, GLM-5, Grok 3 |
| D级 | 1.50x | Llama 4 Maverick, GPT-oss, 其他小型/旧模型 |

推理型模型（如 DeepSeek R1）额外 +0.15（thinking token 开销）。

### 提示词完整度系数

**关键发现**：完整的规格书可将执行端 token 消耗降至正常预估的 30%。

| 等级 | 系数 | 说明 |
|------|------|------|
| 模糊口头描述 | 1.0x | "帮我做个XX" |
| 有功能列表但不详细 | 0.7x | 有功能点但缺少细节 |
| 结构化需求文档 | 0.5x | 有数据模型、API设计 |
| 完整规格书 | 0.3x | thinking partner产出，可直接实现 |



## 自定义

### 修改定价

编辑 `references/pricing.json`，按需添加或更新模型定价：

```json
{
  "models": {
    "my-model": {
      "provider": "Provider Name",
      "input_per_1M": 1.00,
      "output_per_1M": 4.00,
      "context_window": 128000,
      "currency": "USD",
      "note": "Custom model"
    }
  }
}
```

### 调整基础量

编辑 `references/base_tokens.json`，修改项目类型和功能的 token 范围。

### 更新模型智能度

编辑 `references/model_intelligence.json`，添加新模型或更新 benchmark 分数。
建议每 1-2 月检查 SWE-bench、LiveCodeBench、Terminal-Bench 排行榜。

### 降低 token 消耗的最佳实践

**最有效的方式**：先在 thinking partner（Claude.ai / ChatGPT 网页版）中完成需求设计，
产出完整规格书，再交给执行端实现。这可将 token 消耗降至正常预估的 30%。

## 支持的工具检测

- Claude Code (CLI + VSCode 插件)
- Cursor
- Hermes Agent
- everything-claude-code
- 自定义 harness 配置

## License

MIT

---

**注意**：所有预估均为经验值，实际消耗受多种因素影响（提示词质量、模型版本、
上下文管理策略等）。仅供参考，不作为精确预算依据。
