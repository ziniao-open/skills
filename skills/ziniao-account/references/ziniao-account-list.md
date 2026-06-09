# account list

> **前置条件：** 先阅读 [`../ziniao-shared/SKILL.md`](../../ziniao-shared/SKILL.md)。

查询账号列表。只读操作，支持分页和按名称搜索。

## 命令

```bash
ziniao-cli account list
ziniao-cli account list --format table
ziniao-cli account list --name "Rosehut"
ziniao-cli account list --page-all --page-size 50
```

## 参数

| 参数 | 必填 | 说明 |
|------|------|------|
| `--name` | 否 | 按名称搜索 |
| `--page-size` | 否 | 每页条数（默认 20） |
| `--page-all` | 否 | 自动翻页获取全部 |
| `--page-limit` | 否 | 最大翻页数（默认 10，0 为不限） |
| `--page-delay` | 否 | 翻页间隔毫秒数（默认 200） |
| `--format` | 否 | json \| table \| csv |
| `--jq` | 否 | jq 过滤表达式 |

## 参考

- [ziniao-account](../SKILL.md) — 账号管理全部命令
