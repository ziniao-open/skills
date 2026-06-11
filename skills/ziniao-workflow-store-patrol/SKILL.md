---
name: ziniao-workflow-store-patrol
version: 1.0.0
description: "店铺巡检工作流：编排 store list + store open + page visit + page screenshot 完成店铺状态检查和截图巡检。适用于定期检查店铺页面状态、批量截图存档。"
metadata:
  requires:
    bins: ["ziniao-cli"]
---

# 店铺巡检工作流

**CRITICAL — 开始前 MUST 先用 Read 工具读取 [`../ziniao-shared/SKILL.md`](../ziniao-shared/SKILL.md)**

## 适用场景

- "帮我检查所有店铺状态"
- "批量截图所有店铺首页"
- "巡检一下店铺"
- "看看哪些店铺有问题"

## 前置条件

- 紫鸟浏览器客户端已启动
- 已配置 apiKey（`ziniao-cli config init`）

## 工作流

```
store list ──► 获取所有店铺
    │
    ├──► 对每个店铺:
    │       store open --id <id> --url <target>
    │       page wait-nav --store-id <id>
    │       page screenshot --store-id <id> --full-page
    │       store close --id <id>
    │
    └──► 汇总报告
```

### Step 1: 获取店铺列表

```bash
ziniao-cli store list --format table
```

### Step 2: 逐个巡检

对每个店铺执行：

```bash
# 打开店铺并导航到目标页面
ziniao-cli store open --id <storeId> --url "https://www.amazon.com"

# 等待页面加载
ziniao-cli page wait-nav --store-id <storeId>

# 全页截图
ziniao-cli page screenshot --store-id <storeId> --full-page

# 可选：提取页面数据
ziniao-cli page extract --store-id <storeId>

# 关闭店铺
ziniao-cli store close --id <storeId>
```

### Step 3: 汇总

将结果整理为报告：

```
## 店铺巡检报告

| 店铺 | 状态 | 截图 | 备注 |
|------|------|------|------|
| Rosehut | 正常 | 已截图 | — |
| US Store | 异常 | 已截图 | 页面加载超时 |
```

## 高级用法

### 无头模式巡检（更轻量）

```bash
ziniao-cli store open --id <storeId> --url "https://www.amazon.com" --headless
```

### 等待 SPA 页面完全加载

```bash
ziniao-cli page visit --store-id <storeId> --url "https://sellercentral.amazon.com" --wait-until networkidle
```

### 健康检查：检测异常页面

```bash
# 提取页面文本，判断是否出现错误提示（403、验证码、封号）
ziniao-cli page content --store-id <storeId> --content-format text

# 查询特定异常元素是否存在
ziniao-cli page query --store-id <storeId> --selector ".captcha-container, .error-page, #auth-warning"
```

### 部分巡检（按关键词或数量限制）

```bash
# 只巡检名称含 "US" 的店铺
ziniao-cli store list --keyword "US"

# 只巡检前 5 个店铺（调试用）
ziniao-cli store list --limit 5
```

### 截图保存到指定路径

```bash
ziniao-cli page screenshot --store-id <storeId> --full-page --path "./patrol/store-<storeId>.png"
```

### 多页面巡检（单店铺访问多个 URL）

```bash
ziniao-cli store open --id <storeId>
ziniao-cli page visit --store-id <storeId> --url "https://sellercentral.amazon.com" --wait-until networkidle
ziniao-cli page screenshot --store-id <storeId> --path "./patrol/<storeId>-seller-central.png"
ziniao-cli page visit --store-id <storeId> --url "https://www.amazon.com/dp/ASIN" --wait-until networkidle
ziniao-cli page screenshot --store-id <storeId> --path "./patrol/<storeId>-listing.png"
ziniao-cli store close --id <storeId>
```

### 使用 automation run 简化单店操作

```bash
ziniao-cli automation run --steps '[
  {"type": "visit", "url": "https://www.amazon.com", "waitUntil": "networkidle"},
  {"type": "screenshot", "fullPage": true, "path": "./patrol/<storeId>.png"}
]'
```

### 超时控制

```bash
# 页面加载慢时增加超时（默认 30s）
ziniao-cli page wait-nav --store-id <storeId> --timeout 60000
ziniao-cli page screenshot --store-id <storeId> --timeout 15000
```

## 注意事项

- 逐个操作店铺，不要并发打开多个
- 每个店铺操作完成后关闭再开下一个
- 如果某个店铺操作失败，记录错误并继续下一个
- 页面加载可能较慢，`wait-nav` 默认超时 30 秒
- 无头模式巡检更快但部分页面可能行为不同，按需选择

## 参考

- [ziniao-store](../ziniao-store/SKILL.md) — 店铺管理
- [ziniao-page](../ziniao-page/SKILL.md) — 页面操作
- [ziniao-shared](../ziniao-shared/SKILL.md) — 认证和全局参数
