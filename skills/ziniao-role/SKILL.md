---
name: ziniao-role
version: 1.0.0
description: "紫鸟角色管理：角色的查询、创建、修改、分配和权限查看。当用户需要查看角色列表、创建/修改角色、将角色分配给员工或查看角色成员时使用。"
metadata:
  requires:
    bins: ["ziniao-cli"]
---

# 角色管理

**CRITICAL — 开始前 MUST 先用 Read 工具读取 [`../ziniao-shared/SKILL.md`](../ziniao-shared/SKILL.md)**

管理紫鸟 ERP 中的角色（权限组）。角色决定员工能看到哪些功能、能操作哪些店铺。
创建员工时需要传入 `--role-id`，可通过 `role list` 获取。

## Shortcuts

| 命令 | 说明 |
|------|------|
| `role list` | 查询所有角色 |
| `role detail --id` | 查看角色详情（含权限列表） |
| `role create --name --identity-id --permission-ids` | 创建角色 |
| `role update --id --name --identity-id --permission-ids` | 修改角色（需全量提交，不传的字段可能被清空） |
| `role assign --role-id --staff-id` | 将角色分配给员工（`--staff-id` 支持批量） |
| `role staff-roles --staff-id` | 查看某员工已分配的角色列表 |
| `role permissions --identity-id` | 查看指定身份的可用权限项 |

## 核心场景

### 查看角色与权限

```bash
# 查询所有角色（先获取 role-id）
ziniao-cli role list --format table

# 查看某角色详情
ziniao-cli role detail --id 123

# 查看系统支持的全部权限项（创建角色前先查）
ziniao-cli role permissions --identity-id 2
```

### 创建与修改角色

```bash
# 创建角色（权限使用权限 ID，多个权限用逗号分隔）
ziniao-cli role create --name "店铺运营" --identity-id 2 --permission-ids "100,101" --desc "负责日常店铺操作"

# 修改角色（需全量提交，建议先 role detail 查看当前值）
ziniao-cli role update --id 123 --name "高级运营" --identity-id 2 --permission-ids "100,101,102"
```

### 分配角色给员工

```bash
# 将角色分配给单个员工
ziniao-cli role assign --role-id 123 --staff-id 456

# 批量分配给多人
ziniao-cli role assign --role-id 123 --staff-id "456,789,101"

# 查看某员工已分配的角色
ziniao-cli role staff-roles --staff-id 456 --format table
```

### 典型工作流：新建角色并批量分配

```bash
# 1. 查看系统已有角色，避免重复创建
ziniao-cli role list --format table

# 2. 查看普通成员可用权限项
ziniao-cli role permissions --identity-id 2 --jq '.[] | .id'

# 3. 创建新角色
ziniao-cli role create --name "跨境运营" --identity-id 2 --permission-ids "100,101,102"

# 4. 获取新角色 ID（从上一步输出的 data.id 取）
# 5. 批量分配给员工
ziniao-cli role assign --role-id <new-id> --staff-id "201,202,203"
```

## 通用 api 覆盖

```bash
ziniao-cli api /superbrowser/rest/v1/erp/per/role/list
ziniao-cli api /superbrowser/rest/v1/erp/per/role/detail --data '{"roleId":"xxx"}'
ziniao-cli api /superbrowser/rest/v1/erp/per/role/add --data '{"roleName":"xxx","identityId":2,"permissionIds":[100,101]}'
ziniao-cli api /superbrowser/rest/v1/erp/per/role/edit --data '{"roleId":"xxx","roleName":"yyy","identityId":2,"permissionIds":[100,101]}'
ziniao-cli api /superbrowser/rest/v1/erp/staff/change/role --data '{"roleId":"xxx","staffIds":["yyy"]}'
ziniao-cli api /superbrowser/rest/v1/erp/staff/role/list --data '{"staffId":"yyy"}'
ziniao-cli api /superbrowser/rest/v1/erp/per/permission/list --data '{"identityId":2}'
```
