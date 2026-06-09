# store list

> **前置条件：** 先阅读 [`../ziniao-shared/SKILL.md`](../../ziniao-shared/SKILL.md)。紫鸟浏览器必须已启动。

列出所有店铺。只读操作。

## 命令

```bash
ziniao-cli store list
ziniao-cli store list --format table
ziniao-cli store list --keyword "Rose" --limit 10
ziniao-cli store list --all
```

## 参数

| 参数 | 必填 | 说明 |
|------|------|------|
| `--page` | 否 | 页码 |
| `--limit` | 否 | 每页数量 |
| `--all` | 否 | 获取全部店铺 |
| `--keyword` | 否 | 按关键词筛选 |
| `--type` | 否 | 店铺列表类型 |
| `--format` | 否 | 输出格式: json \| table |
| `--jq` | 否 | jq 过滤表达式 |

## 输出字段

| 字段 | 说明 |
|------|------|
| storeId | 店铺 ID（后续操作需要） |
| storeName | 店铺名称 |
| platformName | 平台名称 |
| ip | 绑定的 IP |

## 提示

- storeId 是后续所有页面操作（page visit/click/screenshot 等）的必填参数
- 建议先用 `store list --format table` 查看所有店铺

## 参考

- [ziniao-store](../SKILL.md) — 店铺管理全部命令
