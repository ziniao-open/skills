# department create

> **前置条件：** 先阅读 [`../ziniao-shared/SKILL.md`](../../ziniao-shared/SKILL.md) 了解认证和安全规则。

新增部门。写入操作。

## 命令

```bash
# 创建顶级部门
ziniao-cli department create --name "市场部"

# 创建子部门（hierarchy 自动推算）
ziniao-cli department create --name "华东区" --parent-id 15868464646076
```

## 参数

| 参数 | 必填 | 说明 |
|------|------|------|
| `--name` | 是 | 部门名称 |
| `--parent-id` | 否 | 父部门 ID（默认 "0" 表示顶级部门） |

## 智能推算

- `hierarchy` 由 CLI 自动推算：无 `--parent-id` 则 `hierarchy=0`；有则查父部门层级 +1
- 用户无需关心 `hierarchy` 参数

## 参考

- [ziniao-department](../SKILL.md) — 部门管理全部命令
