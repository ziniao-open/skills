---
name: ziniao-skill-maker
version: 1.0.0
description: "创建 ziniao-cli 的自定义 Skill。当用户需要把紫鸟 API 操作封装成可复用的 Skill（包装原子 API 或编排多步流程）时使用。"
metadata:
  requires:
    bins: ["ziniao-cli"]
---

# Skill Maker

基于 ziniao-cli 创建新 Skill。Skill = 一份 `SKILL.md`，教 AI 用 CLI 命令完成任务。

## CLI 核心能力

```bash
ziniao-cli <service> <command>                   # 快捷命令
ziniao-cli api [METHOD] <path> [--data/--format] # 任意服务端 API
ziniao-cli zclaw invoke <tool> [--args]          # 任意 ZClaw 工具
ziniao-cli store/page <command>                  # ZClaw 快捷命令
```

优先级：快捷命令 > 通用 api/zclaw invoke。

## 调研 API

```bash
# 1. 查看已有快捷命令
ziniao-cli department --help
ziniao-cli staff --help

# 2. 查看 ZClaw 工具
ziniao-cli zclaw tools

# 3. 通用 api 裸调
ziniao-cli api /superbrowser/rest/v1/erp/per/role/list
```

如果命令不够用，使用 [ziniao-openapi-explorer](../ziniao-openapi-explorer/SKILL.md) 查找 API 路径。

## SKILL.md 模板

文件放在 `skills/ziniao-<name>/SKILL.md`：

```markdown
---
name: ziniao-<name>
version: 1.0.0
description: "<功能描述>。当用户需要<触发场景>时使用。"
metadata:
  requires:
    bins: ["ziniao-cli"]
---


# <标题>

**CRITICAL — 开始前 MUST 先用 Read 工具读取 [`../ziniao-shared/SKILL.md`](../ziniao-shared/SKILL.md)**

## 命令

\```bash
# 单步操作
ziniao-cli api POST /superbrowser/rest/v1/erp/xxx --data '{...}'

# 多步编排
# Step 1: ...（记录返回的 xxx_id）
# Step 2: 使用 Step 1 的 xxx_id
\```

## 参考

- [ziniao-shared](../ziniao-shared/SKILL.md) — 认证和全局参数
```

## 关键原则

- **description 决定触发** — 包含功能关键词 + "当用户需要...时使用"
- **CRITICAL 依赖** — 所有 Skill 必须强制读取 ziniao-shared
- **安全** — 写入操作前确认用户意图
- **编排** — 说明步骤间的数据传递和失败处理
