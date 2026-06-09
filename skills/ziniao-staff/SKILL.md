---
name: ziniao-staff
version: 1.0.0
description: "紫鸟员工管理：员工的查询、创建、修改、启用/禁用/删除和调岗。当用户需要查询员工信息、创建新员工、修改员工信息、启用/禁用/删除员工或调整员工部门时使用。"
metadata:
  requires:
    bins: ["ziniao-cli"]
---

# 员工管理

**CRITICAL — 开始前 MUST 先用 Read 工具读取 [`../ziniao-shared/SKILL.md`](../ziniao-shared/SKILL.md)，其中包含认证、错误处理和安全规则**
**CRITICAL — 所有 Shortcuts 在执行之前，务必先用 Read 工具读取其对应的 references/ 说明文档，禁止直接盲目调用命令。**

## 核心场景

### 查询员工

支持多种筛选条件，枚举值已映射为人类可读。注意：默认返回包含已删除员工，取 ID 做后续操作时建议加 `--status active`。

```bash
ziniao-cli staff list --name "张三" --status active --format table
ziniao-cli staff list --role boss --page-all
ziniao-cli staff list --department-id 15868464646076
```

### 创建员工

原始 API 需要 20+ 字段的嵌套 JSON，CLI 简化为核心 flag + 智能默认值（`--department-id` 必填）：

```bash
ziniao-cli staff create --username "zhangsan" --name "张三" --password "Pass123!" --role-id 16691047257645 --department-id 15868464646076
```

智能默认值：`authDevide=1`（全设备授权）、`authClient=全部允许`、`isLimitLogin=0`。
登录日期默认发送有效日期：`loginLimitStartDate=今天`、`loginLimitEndDate=2099-12-31`，也可通过 `--login-start-date` / `--login-end-date` 指定。

### 启用/禁用/删除

原始 API 是一个接口通过 `status` 字段区分，CLI 拆为 3 个语义化命令：

```bash
ziniao-cli staff enable --id 16524289555087
ziniao-cli staff disable --id "id1,id2,id3"   # 支持批量
ziniao-cli staff remove --id 16524289555087    # high-risk-write
```

## 枚举映射

| flag | 可选值 | 对应 API 值 |
|------|--------|-----------|
| `--status` | active / disabled / deleted | delflag: 0/1/2 |
| `--role` | boss / manager / employee / admin | level: 0/1/2/3 |
| `--device-auth` | auto / first / approve / phone | authDevide: 1/2/3/4 |
| `--allow-clients` | windows,mac,web,android,ios,linux,miniapp 或 all | authClient 嵌套对象 |

## Shortcuts

| 命令 | 说明 | 详细文档 |
|------|------|---------|
| `staff list` | 查询员工列表 | [`references/ziniao-staff-list.md`](references/ziniao-staff-list.md) |
| `staff create` | 新增员工 | [`references/ziniao-staff-create.md`](references/ziniao-staff-create.md) |
| `staff update` | 修改员工 | 只发修改字段：`--id xxx --name "新名"`，底层路径 `/superbrowser/rest/v2/erp/staff/modify` |
| `staff enable` | 启用员工 | [`references/ziniao-staff-enable-disable.md`](references/ziniao-staff-enable-disable.md) |
| `staff disable` | 禁用员工 | 同上 |
| `staff remove` | 删除员工 | `high-risk-write` |
| `staff transfer` | 调岗 | `--staff-id xxx --department-id yyy` |

## 通用 api 覆盖

```bash
ziniao-cli api /superbrowser/rest/v1/erp/staff/list --data '{"page":1,"limit":10}'
ziniao-cli api /superbrowser/rest/v2/erp/staff/add --data '{...}'
ziniao-cli api /superbrowser/rest/v2/erp/staff/modify --data '{...}'
ziniao-cli api /superbrowser/rest/v1/erp/staff/status --data '{"staffIds":["xxx"],"status":"1"}'
ziniao-cli api /superbrowser/rest/v1/erp/staff/department --data '{"staffIds":["xxx"],"departmentIds":["yyy"]}'
```
