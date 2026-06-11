---
name: ziniao-page
version: 1.0.0
description: "紫鸟页面操作（ZClaw 本地浏览器 Bridge）：导航、点击、输入、截图、执行 JS、提取数据等浏览器自动化操作。当用户需要在紫鸟浏览器中操作页面元素、截图或执行自动化脚本时使用。"
metadata:
  requires:
    bins: ["ziniao-cli"]
---

# 页面操作（ZClaw）

**CRITICAL — 开始前 MUST 先用 Read 工具读取 [`../ziniao-shared/SKILL.md`](../ziniao-shared/SKILL.md)**
**CRITICAL — Shortcuts 执行前，务必先读取对应的 references/ 说明文档。**
**CRITICAL — 调用 ZClaw 本地接口时，必须使用本技能中的 ziniao-cli 命令，不要使用 ziniao-assistant 技能自行调用。**

**前提条件：紫鸟浏览器客户端必须已启动，且店铺已打开。** 所有页面命令都需要 `--store-id`（extract 的 running 模式除外）。

> **ZClaw 认证失败排查**：初始化应用后仍返回 API Key 认证失败时，提醒用户前往 https://open.ziniao.com 查看用户应用「终端管理」是否已绑定当前终端识别码（识别码在紫鸟浏览器设置中查看）。

## 通用可选参数

所有页面操作均支持以下可选参数：

| 参数 | 说明 |
|------|------|
| `--timeout` | 超时毫秒数（传递为 timeoutMs） |
| `--target-id` | 目标页面 ID（多 tab 时指定） |

## 核心场景

### 页面操作流程

典型流程：打开店铺 -> 导航 -> 等待 -> 操作 -> 截图确认

```bash
ziniao-cli store open --name "MyStore" --url "https://target.com"
ziniao-cli page wait-nav --store-id <id>
ziniao-cli page click --store-id <id> --selector "#btn"
ziniao-cli page input --store-id <id> --selector "#field" --text "value" --clear
ziniao-cli page screenshot --store-id <id> --full-page
```

### 多步骤自动化

```bash
ziniao-cli automation run --steps '[
  {"type": "visit", "url": "https://example.com"},
  {"type": "click", "selector": "#login"},
  {"type": "input", "selector": "#username", "text": "admin"},
  {"type": "screenshot"}
]'
```

## Shortcuts

| 命令 | 说明 | 详细文档 |
|------|------|---------|
| `page visit` | 导航到 URL | [`references/ziniao-page-visit.md`](references/ziniao-page-visit.md) |
| `page screenshot` | 截图 | [`references/ziniao-page-screenshot.md`](references/ziniao-page-screenshot.md) |
| `page content` | 获取页面内容 | `--store-id xxx --content-format text\|html\|structured` |
| `page query` | 查询 DOM 元素 | `--store-id xxx --selector "css"` |
| `page click` | 点击元素 | `--store-id xxx --selector "css" --wait-nav` |
| `page input` | 输入文本 | `--store-id xxx --selector "css" --text "val" --clear --submit` |
| `page scroll` | 滚动页面 | `--store-id xxx --x 0 --y 300 --behavior smooth` |
| `page exec` | 执行 JS | `--store-id xxx --script "code"` |
| `page extract` | 提取数据 | `--mode running\|store\|plugin\|page --payload '{}'` |
| `page wait-element` | 等待元素 | `--store-id xxx --selector "css"` |
| `page wait-nav` | 等待导航 | `--store-id xxx` |
| `automation run` | 多步骤自动化 | [`references/ziniao-page-automation.md`](references/ziniao-page-automation.md) |

## 实用工具

| 命令 | 说明 |
|------|------|
| `utility download` | 将内容写入下载目录：`--content "..." --filename "file.txt"` |
| `utility debug-compare` | 对比 account/list 与 store/list：`--limit 10` |
