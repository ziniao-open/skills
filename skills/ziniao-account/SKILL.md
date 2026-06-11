---
name: ziniao-account
version: 1.1.0
description: "紫鸟账号（店铺）管理：店铺账号的查询、创建、修改、删除、授权和标签管理。当用户需要查看店铺账号列表、创建/修改/删除账号、管理账号授权或标签时使用。"
metadata:
  requires:
    bins: ["ziniao-cli"]
---

# 账号（店铺）管理

**CRITICAL — 开始前 MUST 先用 Read 工具读取 [`../ziniao-shared/SKILL.md`](../ziniao-shared/SKILL.md)**
**CRITICAL — Shortcuts 执行前，务必先读取对应的 references/ 说明文档。**

紫鸟的"账号"指浏览器店铺账号（store），不是员工用户。本模块共覆盖 22 个接口，全部已有快捷命令。

> **注意区分：** `account` 命令通过**服务端 API** 管理店铺账号（创建、删除、授权、标签）。如需控制本地浏览器窗口（打开/关闭/导航/截图），请使用 `store` + `page` 命令（走本地 ZClaw Bridge）。

> **获取店铺列表的选择：**
> - 成员账号没有服务端 `account list` 权限，应使用 `store list`（本地 ZClaw Bridge）获取店铺列表。
> - Boss 账号如果目的是打开店铺浏览器，也应优先使用 `store list` 获取本地可用店铺，而非 `account list`。
> - `account list` 适用于需要管理店铺元数据（标签、授权、批量操作）的场景。
> - 成员类型可通过 `ziniao-cli config show` 结果中的 `isBoss` 字段判断。

## Shortcuts — 账号 CRUD

| 命令 | 说明 | 详细文档 |
|------|------|---------|
| `account list` | 查询账号列表 | [`references/ziniao-account-list.md`](references/ziniao-account-list.md) |
| `account create` | 创建账号 | [`references/ziniao-account-create.md`](references/ziniao-account-create.md) |
| `account update` | 编辑账号基础信息 | 只发修改字段：`--id xxx --name "新名"` |
| `account delete` | 删除账号（高风险） | `--id xxx`，逗号分隔可批量，需 `--yes` |

```bash
# 查询
ziniao-cli account list --name "某店" --format table
ziniao-cli account list --page-all

# 创建
ziniao-cli account create --name "我的店铺" --site-id 1 --remark "测试"

# 修改
ziniao-cli account update --id 123 --name "新名称" --site-id 1

# 删除（批量）
ziniao-cli account delete --id "101,102,103" --yes
```

## Shortcuts — 授权管理

| 命令 | 说明 |
|------|------|
| `account auth-add` | 授权员工访问店铺（`--store-id` 和 `--staff-id` 均支持逗号分隔批量） |
| `account auth-remove` | 撤销员工访问权限（`--store-id` 和 `--staff-id` 均支持逗号分隔批量） |
| `account auth-clean` | 清空店铺所有员工授权（高风险） |
| `account auth-list` | 查询某员工被授权的店铺列表 |
| `account auth-users` | 查询某店铺已授权的员工列表 |

```bash
# 批量授权（把 3 个店铺同时授权给 3 个员工）
ziniao-cli account auth-add --store-id "111,222,333" --staff-id "201,202,203"

# 撤销单人
ziniao-cli account auth-remove --store-id 111 --staff-id 201

# 查看某员工被授权的所有店铺
ziniao-cli account auth-list --user-id 201 --page-all --format table

# 清空授权（高风险）
ziniao-cli account auth-clean --store-id 111 --yes
```

## Shortcuts — 标签管理

| 命令 | 说明 |
|------|------|
| `account tag-list` | 查询企业下所有标签 |
| `account tag-of-store --store-id` | 查询某店铺绑定的标签列表 |
| `account tag-create --name` | 创建标签 |
| `account tag-rename --id --name` | 重命名标签 |
| `account tag-delete --id` | 删除标签（高风险） |
| `account tag-bind --tag-id --store-id` | 绑定标签到店铺（`--store-id` 支持批量） |
| `account tag-unbind --tag-id --store-id` | 解绑（`--store-id` 支持批量） |
| `account tag-replace --store-id --tag-ids` | 全量替换店铺的标签集合 |
| `account tag-clear --store-id` | 清空店铺所有标签（高风险） |
| `account tag-remove --tag-id --store-id` | 从指定店铺移除该标签（高风险，`--store-id` 支持批量） |

底层接口与请求体字段必须严格按接口文档发送，不要补发未记录的别名字段。

| 命令 | API | 请求体字段（不含公共 `companyId`） |
|------|-----|------------------------------------------|
| `account tag-list` | `/superbrowser/rest/v1/erp/tag/list` | 无 |
| `account tag-of-store --store-id` | `/superbrowser/rest/v1/store-tag/list` | `storeId`, 可选 `withSystem` |
| `account tag-create --name` | `/superbrowser/rest/v1/store-tag/add` | `tagName` |
| `account tag-rename --id --name` | `/superbrowser/rest/v1/store-tag/rename` | `tid`, `name` |
| `account tag-delete --id` | `/superbrowser/rest/v1/store-tag/delete` | `tagIds` |
| `account tag-bind --tag-id --store-id` | `/superbrowser/rest/v1/store-tag/bind` | `tagIds`, `storeIds` |
| `account tag-unbind --tag-id --store-id` | `/superbrowser/rest/v1/store-tag/unbind` | `tagIds`, `storeIds` |
| `account tag-replace --store-id --tag-ids` | `/superbrowser/rest/v1/store-tag/replace` | `tagIds`, `storeIds` |
| `account tag-clear --store-id` | `/superbrowser/rest/v1/store-tag/clear` | `storeIds` |
| `account tag-remove --tag-id --store-id` | `/superbrowser/rest/v1/store-tag/remove` | `tagIds`, `storeIds` |

```bash
# 查看所有标签
ziniao-cli account tag-list --format table

# 查看某店铺已有标签
ziniao-cli account tag-of-store --store-id 101

# 创建标签后批量绑定到多个店铺
ziniao-cli account tag-create --name "重点店铺"
ziniao-cli account tag-bind --tag-id 99 --store-id "101,102,103"

# 全量替换（将店铺 101 的标签换成 99 和 100）
ziniao-cli account tag-replace --store-id 101 --tag-ids "99,100"

# 从多个店铺移除某个标签
ziniao-cli account tag-remove --tag-id 99 --store-id "101,102" --yes
```

## 通用 api 覆盖（仅剩的接口）

```bash
# 缓存清理（仅此接口无快捷命令）
ziniao-cli api /superbrowser/rest/v1/erp/store/deletecookie --data '{"removeStoreId":"xxx","removeType":1}'
# removeType: 1=清除所有缓存, 2=仅保留 cookie, 3=仅保留二步验证状态

# 账号授权用户列表 / 用户有权限的账号列表
ziniao-cli api /superbrowser/rest/v1/erp/store/user/list --data '{"storeIdList":["xxx"],"page":1,"limit":10}'
ziniao-cli api /superbrowser/rest/v1/erp/user/stores --data '{"userId":"xxx","page":1,"limit":10}'
```
