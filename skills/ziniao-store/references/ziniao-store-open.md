# store open

> **前置条件：** 先阅读 [`../ziniao-shared/SKILL.md`](../../ziniao-shared/SKILL.md)，再检查店铺操作所需状态：
>
> ```bash
> ziniao-cli doctor
> ```
>
> `doctor` 明细确认通过后直接执行 `store open`，不得无条件启动或登录。检查未通过时，严格按照 `ziniao-shared` 的「ZClaw 客户端与 Bridge 就绪」流程处理；只有客户端未登录或 `store open` 明确返回会话缺失/过期时，才允许登录一次，随后重新检查并只重试一次。API Key、终端或账号认证失败时立即停止。

打开或复用店铺浏览器窗口；带 `--url` 时导航到目标页面。

> 首次进入目标页面时，优先使用 `store open --url <url>`，即使店铺为 `reused: true` 也会直接导航当前页面。已建立会话后的后续 URL 跳转使用 `page visit`。同一个 URL 不得连续执行 `store open --url` 与 `page visit`。

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
