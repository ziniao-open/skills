# device list

> **前置条件：** 先阅读 [`../ziniao-shared/SKILL.md`](../../ziniao-shared/SKILL.md)。

查询设备列表。只读操作。

底层 API：`/superbrowser/rest/v1/erp/ip/page`

## 命令

```bash
ziniao-cli device list
ziniao-cli device list --format table
ziniao-cli device list --page-all
```

## 参数

| 参数 | 必填 | 说明 |
|------|------|------|
| `--page-size` | 否 | 每页条数（默认 20） |
| `--page-all` | 否 | 自动翻页获取全部 |
| `--page-limit` | 否 | 最大翻页数（默认 10，0 为不限） |
| `--page-delay` | 否 | 翻页间隔毫秒数（默认 200） |
| `--format` | 否 | json \| table \| csv |

## 参考

- [ziniao-device](../SKILL.md) — 设备管理全部命令
