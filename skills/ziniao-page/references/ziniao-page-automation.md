# automation run

> **前置条件：** 先阅读 [`../ziniao-shared/SKILL.md`](../../ziniao-shared/SKILL.md)。紫鸟浏览器必须已启动，且店铺已打开。

执行多步骤自动化流程。将多个页面操作编排为一条命令执行，减少多次调用的开销。

## 命令

```bash
ziniao-cli automation run --steps '[
  {"type": "visit", "url": "https://example.com", "waitUntil": "networkidle"},
  {"type": "wait", "selector": "#login"},
  {"type": "click", "selector": "#login"},
  {"type": "input", "selector": "#username", "text": "admin", "clear": true},
  {"type": "input", "selector": "#password", "text": "pass123", "clear": true},
  {"type": "click", "selector": "#submit", "waitForNavigation": true},
  {"type": "screenshot", "fullPage": true, "path": "./result.png"}
]'
```

## 参数

| 参数 | 必填 | 说明 |
|------|------|------|
| `--steps` | 是 | 步骤 JSON 数组 |

## 步骤类型及字段

### visit — 导航到 URL

| 字段 | 必填 | 说明 |
|------|------|------|
| `url` | 是 | 目标 URL |
| `waitUntil` | 否 | 等待条件: domcontentloaded / load / networkidle |
| `timeoutMs` | 否 | 超时毫秒数 |

### click — 点击元素

| 字段 | 必填 | 说明 |
|------|------|------|
| `selector` | 是 | CSS 选择器 |
| `waitForNavigation` | 否 | 点击后等待导航完成 |
| `timeoutMs` | 否 | 超时毫秒数 |

### input — 输入文本

| 字段 | 必填 | 说明 |
|------|------|------|
| `selector` | 是 | CSS 选择器 |
| `text` | 是 | 要输入的文本 |
| `clear` | 否 | 输入前清空已有内容 |
| `submit` | 否 | 输入后提交表单 |
| `timeoutMs` | 否 | 超时毫秒数 |

### scroll — 滚动页面

| 字段 | 必填 | 说明 |
|------|------|------|
| `x` | 否 | 水平滚动像素 |
| `y` | 否 | 垂直滚动像素 |
| `selector` | 否 | 滚动目标元素选择器 |
| `behavior` | 否 | 滚动行为: auto / smooth |

### screenshot — 截图

| 字段 | 必填 | 说明 |
|------|------|------|
| `fullPage` | 否 | 全页截图 |
| `path` | 否 | 保存路径 |

### wait — 等待

| 字段 | 必填 | 说明 |
|------|------|------|
| `selector` | 否* | 等待元素出现（与 timeout 二选一） |
| `timeout` | 否* | 延时毫秒数（与 selector 二选一） |
| `timeoutMs` | 否 | 等待超时上限 |

### exec — 执行 JavaScript

| 字段 | 必填 | 说明 |
|------|------|------|
| `script` | 是 | JavaScript 代码 |
| `timeoutMs` | 否 | 超时毫秒数 |

## 使用示例

### 登录流程

```bash
ziniao-cli automation run --steps '[
  {"type": "visit", "url": "https://sellercentral.amazon.com", "waitUntil": "networkidle"},
  {"type": "input", "selector": "#ap_email", "text": "seller@example.com", "clear": true},
  {"type": "input", "selector": "#ap_password", "text": "password123", "clear": true},
  {"type": "click", "selector": "#signInSubmit", "waitForNavigation": true},
  {"type": "screenshot", "fullPage": true}
]'
```

### 滚动加载 + 截图

```bash
ziniao-cli automation run --steps '[
  {"type": "visit", "url": "https://www.amazon.com/dp/B0XXXXX", "waitUntil": "networkidle"},
  {"type": "scroll", "y": 500, "behavior": "smooth"},
  {"type": "wait", "timeout": 1000},
  {"type": "scroll", "y": 1000, "behavior": "smooth"},
  {"type": "wait", "timeout": 1000},
  {"type": "screenshot", "fullPage": true, "path": "./listing-full.png"}
]'
```

### 提取数据后截图确认

```bash
ziniao-cli automation run --steps '[
  {"type": "visit", "url": "https://example.com/dashboard", "waitUntil": "networkidle"},
  {"type": "wait", "selector": ".data-table"},
  {"type": "exec", "script": "document.querySelector('.data-table').style.border = '2px solid red'"},
  {"type": "screenshot", "path": "./dashboard.png"}
]'
```

## 注意事项

- steps 必须为合法 JSON 数组，建议用单引号包裹避免转义问题
- 步骤按顺序执行，任何一步失败会终止后续步骤
- 复杂的条件逻辑（if/else）不适合用 automation run，应拆分为多条单独命令由 Agent 控制

## 参考

- [ziniao-page](../SKILL.md) — 页面操作全部命令
