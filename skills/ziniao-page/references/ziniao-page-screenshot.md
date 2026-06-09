# page screenshot

> **前置条件：** 先阅读 [`../ziniao-shared/SKILL.md`](../../ziniao-shared/SKILL.md)。店铺必须已打开。

对当前页面截图。

## 命令

```bash
# 视口截图
ziniao-cli page screenshot --store-id abc123

# 全页截图
ziniao-cli page screenshot --store-id abc123 --full-page

# 保存到指定路径
ziniao-cli page screenshot --store-id abc123 --path "./output.png"

# 指定超时
ziniao-cli page screenshot --store-id abc123 --timeout 10000
```

## 参数

| 参数 | 必填 | 说明 |
|------|------|------|
| `--store-id` | 是 | 店铺 ID |
| `--full-page` | 否 | 全页截图（默认仅视口） |
| `--path` | 否 | 保存路径 |
| `--timeout` | 否 | 超时毫秒数 |
| `--target-id` | 否 | 目标页面 ID（多 tab 时指定） |

## 参考

- [ziniao-page](../SKILL.md) — 页面操作全部命令
