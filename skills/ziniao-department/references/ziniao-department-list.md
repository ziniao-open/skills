# department list

> **前置条件：** 先阅读 [`../ziniao-shared/SKILL.md`](../../ziniao-shared/SKILL.md) 了解认证和安全规则。

查询部门列表。只读操作。

## 命令

```bash
# 默认 JSON 输出
ziniao-cli department list

# 树形展示
ziniao-cli department list --tree

# 表格输出
ziniao-cli department list --format table

# jq 过滤顶级部门
ziniao-cli department list --jq '.[] | select(.parentId == 0)'
```

## 参数

| 参数 | 必填 | 说明 |
|------|------|------|
| `--tree` | 否 | 以树形结构展示部门层级（按 parentId 关系渲染） |
| `--format` | 否 | 输出格式：json（默认）\| table \| csv |
| `--jq` / `-q` | 否 | jq 过滤表达式 |

## 输出字段

| 字段 | 说明 |
|------|------|
| `id` | 部门 ID |
| `name` | 部门名称 |
| `parentId` | 父部门 ID（0 表示顶级） |
| `hierarchy` | 层级深度 |

## 树形输出格式

当使用 `--tree` 时，按层级渲染：

```
├── 部门名 (id: 123)
│   ├── 子部门 (id: 456)
│   └── 子部门 (id: 789)
└── 其他部门 (id: 101)
```

## 参考

- [ziniao-department](../SKILL.md) — 部门管理全部命令
- [ziniao-shared](../../ziniao-shared/SKILL.md) — 认证和全局参数
