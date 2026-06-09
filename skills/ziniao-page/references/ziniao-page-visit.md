# page visit

> **前置条件：** 先阅读 [`../ziniao-shared/SKILL.md`](../../ziniao-shared/SKILL.md)。店铺必须已打开。

导航到指定 URL。

## 命令

```bash
ziniao-cli page visit --store-id abc123 --url "https://www.amazon.com"

# 等待 networkidle 后再返回
ziniao-cli page visit --store-id abc123 --url "https://www.amazon.com" --wait-until networkidle

# 指定超时
ziniao-cli page visit --store-id abc123 --url "https://www.amazon.com" --timeout 60000
```

## 参数

| 参数 | 必填 | 说明 |
|------|------|------|
| `--store-id` | 是 | 店铺 ID |
| `--url` | 是 | 目标 URL |
| `--wait-until` | 否 | 等待条件: domcontentloaded \| load \| networkidle |
| `--timeout` | 否 | 超时毫秒数 |
| `--target-id` | 否 | 目标页面 ID（多 tab 时指定） |

## 提示

- 导航后建议用 `page wait-nav` 等待页面加载完成
- 可用 `page content` 获取页面内容确认是否到达目标页面
- `--wait-until networkidle` 适合 SPA 页面，等待所有网络请求完成

## 参考

- [ziniao-page](../SKILL.md) — 页面操作全部命令
