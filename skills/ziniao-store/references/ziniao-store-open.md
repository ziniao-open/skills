# store open

> **前置条件：** 先阅读 [`../ziniao-shared/SKILL.md`](../../ziniao-shared/SKILL.md)。紫鸟浏览器必须已启动。

打开店铺浏览器窗口。可选直接导航到 URL。

## 命令

```bash
# 按名称打开
ziniao-cli store open --name "Rosehut"

# 按 ID 打开
ziniao-cli store open --id abc123

# 打开并直接访问 URL
ziniao-cli store open --name "Rosehut" --url "https://www.amazon.com"

# 无头模式打开
ziniao-cli store open --name "Rosehut" --headless

# 隐私模式打开
ziniao-cli store open --name "Rosehut" --privacy
```

## 参数

| 参数 | 必填 | 说明 |
|------|------|------|
| `--name` | 否* | 店铺名称（与 --id 二选一） |
| `--id` | 否* | 店铺 ID（与 --name 二选一） |
| `--expected-name` | 否 | 期望匹配的名称（用于验证） |
| `--url` | 否 | 打开后直接导航到的 URL（对应 launchUrl） |
| `--headless` | 否 | 无头模式 |
| `--privacy` | 否 | 隐私模式 |
| `--window-ratio` | 否 | 窗口比例 |

## 参考

- [ziniao-store](../SKILL.md) — 店铺管理全部命令
