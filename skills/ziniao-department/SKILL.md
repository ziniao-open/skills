---
name: ziniao-department
version: 1.0.0
description: "紫鸟部门管理：部门的查询、创建、修改、删除和排序。当用户需要查看组织架构、创建/修改/删除部门、调整部门排序时使用。"
metadata:
  requires:
    bins: ["ziniao-cli"]
---

# 部门管理

**CRITICAL — 开始前 MUST 先用 Read 工具读取 [`../ziniao-shared/SKILL.md`](../ziniao-shared/SKILL.md)，其中包含认证、错误处理和安全规则**
**CRITICAL — 所有 Shortcuts 在执行之前，务必先用 Read 工具读取其对应的 references/ 说明文档，禁止直接盲目调用命令。**

## 核心场景

### 查看组织架构

用户想了解公司部门结构时，使用 `--tree` 展示层级关系：

```bash
ziniao-cli department list --tree
```

输出树形结构：
```
├── 技术部 (id: 15868464646076)
│   ├── 前端组 (id: 15954943062431)
│   └── 后端组 (id: 15954943062432)
└── 市场部 (id: 15868464646077)
```

### 创建部门

`hierarchy` 由 CLI 自动推算（查父部门层级 +1），用户无需关心：

```bash
# 在已有部门下创建子部门（parentId 必须为已存在的部门 ID，不支持传 0）
ziniao-cli department create --name "华东区" --parent-id 15868464646076
```

> ⚠ `--parent-id` 不能传 0，必须传已存在的父部门 ID。如需查询可用的父部门 ID，先执行 `department list --tree`。

### 危险操作

删除部门是 `high-risk-write`，会要求确认。**删除父部门会级联删除所有子部门，且不可恢复。** 删除前建议先检查是否有子部门或员工：

```bash
ziniao-cli department list --tree          # 检查子部门
ziniao-cli staff list --department-id xxx  # 检查是否有员工
ziniao-cli department delete --id xxx      # 执行删除（需确认）
```

## Shortcuts

| 命令 | 说明 | 详细文档 |
|------|------|---------|
| `department list` | 查询部门列表 | [`references/ziniao-department-list.md`](references/ziniao-department-list.md) |
| `department create` | 新增部门 | [`references/ziniao-department-create.md`](references/ziniao-department-create.md) |
| `department update` | 修改部门 | 只发修改字段：`--id xxx --name "新名"` |
| `department delete` | 删除部门（子部门及员工归属将被清除） | `high-risk-write`，`--yes` 跳过确认 |
| `department reorder` | 调整排序 | `--parent-id xxx --order "id1,id2,id3"` |

## 通用 api 覆盖

所有部门接口也可通过通用 api 命令调用：

```bash
ziniao-cli api /superbrowser/rest/v1/erp/department/list
ziniao-cli api /superbrowser/rest/v1/erp/department/add --data '{"name":"xxx","parentId":"<父部门ID>","hierarchy":"0"}'
ziniao-cli api /superbrowser/rest/v1/erp/department/update --data '{"id":"xxx","name":"新名"}'
ziniao-cli api /superbrowser/rest/v1/erp/department/delete --data '{"id":"xxx"}'
ziniao-cli api /superbrowser/rest/v1/erp/department/order --data '{"parentId":"xxx","order":["id1","id2"]}'
```
