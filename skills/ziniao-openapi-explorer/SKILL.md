---
name: ziniao-openapi-explorer
version: 1.1.0
description: "紫鸟 API 探索：当现有快捷命令无法满足需求时，通过通用 api 命令和已知路径清单探索紫鸟全部 73 个 API 接口。"
metadata:
  requires:
    bins: ["ziniao-cli"]
---

# API 探索

> **前置条件：** 先阅读 [`../ziniao-shared/SKILL.md`](../ziniao-shared/SKILL.md)。

当用户的需求**无法被现有快捷命令覆盖**时，使用本技能查找对应的 API 路径并通过 `ziniao-cli api` 裸调。

## 探索流程

### Step 1: 确认现有命令不足

```bash
ziniao-cli department --help
ziniao-cli staff --help
ziniao-cli account --help
ziniao-cli device --help
ziniao-cli role --help
```

如果已有对应快捷命令，直接使用。

### Step 2: 从路径清单定位 API

**禁止猜测 API 路径**，必须从以下清单中查找。

#### 部门（5 个）✅ 全部已有快捷命令

| 操作 | 路径 | 快捷命令 |
|------|------|---------|
| 列表 | `/superbrowser/rest/v1/erp/department/list` | `department list` |
| 新增 | `/superbrowser/rest/v1/erp/department/add` | `department create` |
| 修改 | `/superbrowser/rest/v1/erp/department/update` | `department update` |
| 删除 | `/superbrowser/rest/v1/erp/department/delete` | `department delete` |
| 排序 | `/superbrowser/rest/v1/erp/department/order` | `department reorder` |

#### 员工（5 个）✅ 全部已有快捷命令

| 操作 | 路径 | 快捷命令 |
|------|------|---------|
| 列表 | `/superbrowser/rest/v1/erp/staff/list` | `staff list` |
| 新增 | `/superbrowser/rest/v2/erp/staff/add` | `staff create` |
| 修改 | `/superbrowser/rest/v2/erp/staff/modify` | `staff update` |
| 状态 | `/superbrowser/rest/v1/erp/staff/status` | `staff enable/disable/remove` |
| 调岗 | `/superbrowser/rest/v1/erp/staff/department` | `staff transfer` |

#### 账号（23 个）— 仍有 4 个需用 `api`

| 分类 | 路径前缀 | 快捷命令 |
|------|---------|---------|
| CRUD | `/superbrowser/rest/v1/erp/store/list\|create\|update/base\|delete` | `account list/create/update/delete` |
| 缓存 | `/superbrowser/rest/v1/erp/store/deletecookie` | `api` |
| 授权 | `/superbrowser/rest/v1/erp/store/auth/add\|delete\|clean`、`/superbrowser/rest/v1/erp/user/stores`、`/superbrowser/rest/v1/erp/store/user/list` | `account auth-*` |
| 授权查询 | `/superbrowser/rest/v1/erp/store/auth/query` | `api` |
| 用户账号 | `/superbrowser/rest/v1/erp/store/list/by/user` | `api` |
| 附加信息 | `/superbrowser/rest/v1/erp/store/addtion` | `api` |
| 标签 | `/superbrowser/rest/v1/erp/tag/list`、`/superbrowser/rest/v1/store-tag/add\|delete\|rename\|bind\|unbind\|replace\|clear\|remove\|list` | `account tag-*` |

#### 设备（11 个）— 仍有 2 个需用 `api`

| 分类 | 路径前缀 | 快捷命令 |
|------|---------|---------|
| 查询 | `/superbrowser/rest/v1/erp/ip/page`、`/superbrowser/rest/v1/erp/proxy/purchased_package_info`、`/superbrowser/rest/v1/erp/ip/historybind` | `device list`（price/history 用 `api`）|
| 套餐 | `/superbrowser/rest/v1/erp/package/new` | `device plan-list` |
| 购买 | `/superbrowser/rest/v1/erp/purchase\|renew` | `device purchase/renew` |
| 自有 | `/superbrowser/rest/v1/erp/ip/self/add/new\|edit/new` | `device add-custom/update-custom` |
| 绑定 | `/superbrowser/rest/v1/erp/ip/bind\|unbind\|renewal` | `device bind/unbind/auto-renew` |

#### 角色（7 个）✅ 全部已有快捷命令

| 操作 | 路径 | 快捷命令 |
|------|------|---------|
| 列表 | `/superbrowser/rest/v1/erp/per/role/list` | `role list` |
| 详情 | `/superbrowser/rest/v1/erp/per/role/detail` | `role detail` |
| 新增 | `/superbrowser/rest/v1/erp/per/role/add` | `role create` |
| 修改 | `/superbrowser/rest/v1/erp/per/role/edit` | `role update` |
| 分配 | `/superbrowser/rest/v1/erp/staff/change/role` | `role assign` |
| 用户角色 | `/superbrowser/rest/v1/erp/staff/role/list` | `role staff-roles` |
| 权限列表 | `/superbrowser/rest/v1/erp/per/permission/list` | `role permissions` |

#### 访问策略（22 个）— 全部需用 `api`

| 分类 | 路径 |
|------|------|
| 规则 | `/superbrowser/rest/v1/erp/security/access_rule/list` |
| 规则 | `/superbrowser/rest/v1/erp/security/access_rule/detail` |
| 规则 | `/superbrowser/rest/v1/erp/security/access_rule/add` |
| 规则 | `/superbrowser/rest/v1/erp/security/access_rule/edit` |
| 规则 | `/superbrowser/rest/v1/erp/security/access_rule/delete` |
| 规则 | `/superbrowser/rest/v1/erp/security/access_rule/active` |
| 规则成员 | `/superbrowser/rest/v1/erp/security/access_rule/change_user` |
| 规则账号 | `/superbrowser/rest/v1/erp/security/access_rule/change_account` |
| 规则账号 | `/superbrowser/rest/v1/erp/security/access_rule/account/bindable_list` |
| 网页 | `/superbrowser/rest/v1/erp/security/access_rule_url/list` |
| 网页 | `/superbrowser/rest/v1/erp/security/access_rule_url/detail` |
| 网页 | `/superbrowser/rest/v1/erp/security/access_rule_url/add` |
| 网页 | `/superbrowser/rest/v1/erp/security/access_rule_url/edit` |
| 网页 | `/superbrowser/rest/v1/erp/security/access_rule_url/delete` |
| 网页 | `/superbrowser/rest/v1/erp/security/access_rule_url/change_group` |
| 网页分组 | `/superbrowser/rest/v1/erp/security/access_rule_url_group/list` |
| 网页分组 | `/superbrowser/rest/v1/erp/security/access_rule_url_group/add` |
| 网页分组 | `/superbrowser/rest/v1/erp/security/access_rule_url_group/edit` |
| 网页分组 | `/superbrowser/rest/v1/erp/security/access_rule_url_group/delete` |
| 账号策略 | `/superbrowser/rest/v1/erp/security/access_rule/list/one_account` |
| 账号策略 | `/superbrowser/rest/v1/erp/security/access_rule/account_ref/add_to_account` |
| 账号策略 | `/superbrowser/rest/v1/erp/security/access_rule/account_ref/remove_from_account` |

### Step 3: 使用 dry-run 探索

```bash
# 先预览请求
ziniao-cli api /superbrowser/rest/v1/erp/per/role/list --dry-run

# 确认后执行
ziniao-cli api /superbrowser/rest/v1/erp/per/role/list
```

## 安全规则

- **禁止猜测** API 路径或参数，必须从清单中查找
- 写入/删除类操作前确认用户意图
- 建议先用 `--dry-run` 预览

## 参考

- [ziniao-shared](../ziniao-shared/SKILL.md) — 认证和全局参数
- [ziniao-account](../ziniao-account/SKILL.md) — 账号/授权/标签快捷命令
- [ziniao-device](../ziniao-device/SKILL.md) — 设备管理快捷命令
- [ziniao-role](../ziniao-role/SKILL.md) — 角色管理快捷命令
- [ziniao-skill-maker](../ziniao-skill-maker/SKILL.md) — 如需将常用 API 固化为新 Skill
