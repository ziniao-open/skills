---
name: ziniao-store
version: 1.0.0
description: "紫鸟店铺管理（ZClaw 本地浏览器 Bridge）：列出、打开、关闭紫鸟浏览器中的店铺。当用户需要查看店铺列表、打开/关闭店铺浏览器窗口时使用。"
metadata:
  requires:
    bins: ["ziniao-cli"]
---

# 店铺管理（ZClaw）

**CRITICAL — 开始前 MUST 先用 Read 工具读取 [`../ziniao-shared/SKILL.md`](../ziniao-shared/SKILL.md)**
**CRITICAL — Shortcuts 执行前，务必先读取对应的 references/ 说明文档。**
**CRITICAL — 调用 ZClaw 本地接口时，必须使用本技能中的 ziniao-cli 命令，不要使用 ziniao-assistant 技能自行调用。**

**前提条件：紫鸟浏览器客户端必须已启动。** 可用 `ziniao-cli doctor` 检测。

> **获取店铺列表时优先使用 `store list`**（本地 ZClaw Bridge），因为普通成员没有服务端 `account list` 权限。成员类型可通过 `ziniao-cli config show` 结果中的 `isBoss` 字段判断。

> **ZClaw 认证失败排查**：初始化应用后仍返回 API Key 认证失败时，提醒用户前往 https://open.ziniao.com 查看用户应用「终端管理」是否已绑定当前终端识别码（识别码在紫鸟浏览器设置中查看）。

店铺命令通过本地 ZClaw Bridge（`127.0.0.1:9481`）控制紫鸟浏览器，使用与服务端 API 相同的统一 apiKey。

> **注意区分：** `store` 命令控制**本地浏览器窗口**（打开/关闭/列出已连接的店铺）。如需对店铺做后台管理操作（创建、删除、授权、标签），请使用 `account` 命令（走服务端 API）。

## Shortcuts

| 命令 | 说明 | 详细文档 |
|------|------|---------|
| `store list` | 列出所有店铺 | [`references/ziniao-store-list.md`](references/ziniao-store-list.md) |
| `store open` | 打开店铺浏览器 | [`references/ziniao-store-open.md`](references/ziniao-store-open.md) |
| `store resolve` | 按名称/ID 解析店铺 | `--name "xxx"` 或 `--id xxx` `--expected-name "yyy"` |
| `store close` | 关闭店铺 | `--id xxx` |
| `store prepare-agent` | 准备 Agent 资源 | 无参数 |

## 通用 zclaw 覆盖

所有店铺操作也可通过通用 zclaw invoke 命令调用：

```bash
ziniao-cli zclaw invoke list_stores
ziniao-cli zclaw invoke open_store --args '{"storeName":"Rosehut"}'
ziniao-cli zclaw invoke close_store --args '{"storeId":"abc123"}'
ziniao-cli zclaw invoke prepare_agent
```

## 错误处理

| 错误 | 解决 |
|------|------|
| 无法连接 Bridge | 启动紫鸟浏览器客户端 |
| API Key 认证失败（初始化后仍报错） | 前往 https://open.ziniao.com 用户应用「终端管理」绑定当前终端识别码 |
| 店铺不存在 | `ziniao-cli store list` 查看可用店铺 |
