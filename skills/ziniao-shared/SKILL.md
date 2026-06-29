---
name: ziniao-shared
version: 1.0.0
description: "紫鸟 CLI 共享基础：应用配置初始化、统一 apiKey 认证、错误处理、输出格式、安全规则。当用户需要第一次配置(`ziniao-cli config init`)、遇到认证/权限问题、或首次使用 ziniao-cli 时触发。"
metadata:
  requires:
    bins: ["ziniao-cli"]
---

# ziniao-cli 共享规则

本技能指导你如何通过 ziniao-cli 操作紫鸟开放平台资源和控制紫鸟浏览器，以及有哪些注意事项。

## 配置初始化

首次使用需运行 `ziniao-cli config init` 完成应用配置。

### 初始化模式

| 命令 | 场景 | 行为 |
|------|------|------|
| `ziniao-cli config init --new` | **AI Agent（推荐）** | 直接进入新建应用流程，输出浏览器链接后轮询等待 |
| `ziniao-cli config init` | 人类用户交互 | 菜单选择：[1] 新建应用 [2] 手动输入 Key |
| `ziniao-cli config init --api-key-stdin` | CI/CD 管道 | 从 stdin 读取已有 API Key |
| `ziniao-cli config init --api-key-stdin --member` | 成员账号 CI | 跳过企业信息获取，仅用于控制浏览器 |
| `ziniao-cli config init --profile <name>` | 多账号 | 指定 profile 名称（不传则自动命名） |

### AI Agent 初始化流程

使用 background 方式执行以下命令，启动后读取 stderr 输出，从中提取浏览器链接并展示给用户：

```bash
# 直接进入新建应用流程（该命令会阻塞直到审核通过、被拒绝或超时 1 小时）
ziniao-cli config init --new
```

输出示例（Boss 账号）：
```
请在浏览器中打开以下链接完成应用创建:

  https://open.ziniao.com/memberAuth?cliRequestId=a1b2c3d4-...&from=cli

⏳ 等待应用创建及审核... (按 Ctrl+C 取消)
⏳ 等待中... 已等待 30s
✓ 审核已通过
⏳ 正在获取企业信息...
✓ 企业 ID: 15393571083459
✓ 配置已保存
```

输出示例（成员账号）：
```
...
✓ 审核已通过
✓ 成员账号，跳过企业信息获取
✓ 配置已保存
```

Agent 应该：
1. 后台执行 `config init --new`
2. 从输出中提取 URL（包含 `memberAuth?cliRequestId=` 的行）
3. 将链接展示给用户，提示在浏览器中打开完成应用创建
4. 等待命令完成（审核通过/拒绝/超时）
5. 如果审核被拒绝，告知用户联系 Boss 审批

所有凭证（apiKey、companyId）存入系统 Keychain，配置文件在 `~/.ziniao-cli/config.json`。

### Boss 与成员账号

初始化时服务端返回 `isBoss` 标识，决定账号权限范围：

| 账号类型 | 服务端 API | ZClaw Bridge（本地浏览器） |
|---------|-----------|--------------------------|
| Boss | ✓ 全部可用 | ✓ 全部可用 |
| 成员 | ✗ 不可用（返回 auth 错误） | ✓ 全部可用 |

成员账号不存储 companyId，所有通过 `api` 命令或服务端快捷命令（account/staff/department/role/device）的请求会被拦截并提示"需要 Boss 权限"。

### 检查配置

```bash
ziniao-cli config show   # 查看当前配置（含 profile 名称）
ziniao-cli config list   # 列出所有 profile
ziniao-cli doctor         # 全面自检（配置 + apiKey + 网络 + ZClaw Bridge）
```

### 多账号切换

支持多个账号配置（profile），通过 `config use` 切换：

```bash
# 列出所有 profile
ziniao-cli config list
# * zhangsan
#   staging

# 切换到指定 profile
ziniao-cli config use staging

# 重命名 profile
ziniao-cli config rename staging production
```

初始化时通过 `--profile` 指定名称，浏览器创建流程会自动用账号用户名命名。

### 删除配置

删除操作会弹出确认提示，`--yes` 可跳过：

```bash
ziniao-cli config remove              # 删除当前 profile（需确认）
ziniao-cli config remove --profile staging  # 删除指定 profile（需确认）
ziniao-cli config remove --yes        # 跳过确认直接删除
```

## 认证

### 认证模型

ziniao-cli 使用**统一 apiKey**（Bearer Token），一个 Key 同时用于：

| 用途 | 地址 | 说明 |
|------|------|------|
| 服务端 API | `sbappstoreapi.ziniao.com` | 部门/员工/账号/设备等业务接口 |
| 本地 ZClaw Bridge | `127.0.0.1:9481` | 紫鸟浏览器店铺/页面操控 |

**没有** OAuth、token 刷新、双身份（user/bot）等复杂机制。apiKey 是静态凭证，不过期。

### ISV 应用权限点

调用服务端 API 前，需在紫鸟开放平台为应用开通对应的权限点。以下是各模块所需的权限点：

| 模块 | 权限点 | 覆盖接口 |
|------|--------|---------|
| 部门员工 | ERP-部门与员工接口 | 部门 CRUD + 员工查询/新增/修改/启禁用（9 个） |
| 部门员工 | ERP-用户的部门变更 | 员工调岗（1 个） |
| 角色权限 | ERP-角色列表查询 | 角色列表 + 用户角色列表（2 个） |
| 角色权限 | ERP-角色详情 | 角色详情（1 个） |
| 角色权限 | ERP-权限列表 | 权限项列表（1 个） |
| 角色权限 | ERP-角色添加、修改权限 | 添加/修改/调整角色（3 个） |
| 设备管理 | ERP-设备查询 | 设备列表 + 历史绑定记录（2 个） |
| 设备管理 | ERP-设备套餐列表查询权限 | 套餐列表（1 个） |
| 设备管理 | ERP-设备绑定权限 | 绑定设备（1 个） |
| 设备管理 | ERP-解绑设备 | 解绑设备（1 个） |
| 设备管理 | ERP-开关自动续费 | 自动续费开关（1 个） |
| 设备管理 | ERP-设备购买与续费权限 | 购买 + 续费（2 个） |
| 设备管理 | ERP-添加自有设备（新） | 添加自有设备（1 个） |
| 设备管理 | ERP-修改自有设备信息（新） | 修改自有设备（1 个） |
| 设备管理 | ERP-查询已购设备价格接口 | 已购设备价格（1 个） |
| 账号管理 | ERP-账号查看权限 | 账号列表/授权查询/用户账号列表/授权用户列表（4 个） |
| 账号管理 | ERP-创建与删除账号权限 | 创建 + 删除账号（2 个） |
| 账号管理 | ERP-编辑账号基础信息 | 编辑账号信息（1 个） |
| 账号管理 | ERP-账号授权权限 | 授权新增 + 授权删除（2 个） |
| 账号管理 | ERP-清除账号授权 | 清除全部授权（1 个） |
| 账号管理 | ERP-清除账号缓存 | 清除缓存（1 个） |
| 账号管理 | ERP-标签列表 | 企业标签列表（1 个） |
| 账号管理 | ERP-查询某用户有权限的账号列表 | 用户有权限的账号（1 个） |
| 账号管理 | ERP-获取附加网站信息 | 附加网站信息（1 个） |
| 账号管理 | 账号标签管理权限 | 标签 CRUD + 绑定/解绑/替换/清空/移除（9 个） |
| 访问策略 | ERP-网页访问权限 | 访问规则/网页/网页分组全部操作（22 个） |

> 如果调用接口返回 `isv.invalid-method`（不存在的方法名），通常是该权限点未开通。前往 [紫鸟开放平台](https://open.ziniao.com) → 应用管理 → 权限管理 中开通。

### 公共参数

每个服务端 API 请求都需要 `companyId`，**CLI 自动注入并强制使用配置值**，无需也不应手动传递。该值在 `config init` 时通过 `/app/builtin/company` 接口自动获取并写入配置；即使 `--data` 中显式传入 `companyId`，CLI 也会用配置值覆盖。

## 两层命令体系

### 第一层：通用 api 命令（覆盖全部 73 个接口）

任何紫鸟服务端 API 都可以通过 `api` 命令调用，无需专门的快捷命令：

```bash
ziniao-cli api <path> [--data '{}'] [--format table] [--jq '.data[]']
ziniao-cli api GET /app/builtin/company
ziniao-cli api /superbrowser/rest/v1/erp/department/list
ziniao-cli api /superbrowser/rest/v1/erp/staff/list --data '{"page":1,"limit":10}' --format table
```

- 默认 POST 方法，支持 GET/POST/PUT/DELETE
- `companyId` 由框架自动注入，并强制覆盖 `--data` 中的同名字段
- `--page-all` 自动翻页（默认最多 10 页，需取全部时配合 `--page-limit 0`）
- `--page-size N` 每页条数（默认 20）
- `--page-limit N` 最大翻页数（默认 10，0 为不限，配合 `--page-all`）
- `--page-delay MS` 翻页间隔毫秒数（默认 200，配合 `--page-all`）
- `--dry-run` 预览请求不执行
- `--jq` 内置 jq 过滤

### 第二层：快捷命令（高频场景优化）

为复杂接口提供命名 flag + 智能默认值：

```bash
ziniao-cli department list --tree
ziniao-cli staff create --username "zhangsan" --name "张三" --password "Pass123!" --role-id 16691047257645
ziniao-cli store list --format table
```

## `account` 与 `store` 的区别

两组命令都涉及"店铺"，但职责和通道完全不同：

| | `account` 命令 | `store` 命令 |
|--|---------------|-------------|
| 通道 | 服务端 API（`sbappstoreapi.ziniao.com`） | 本地 ZClaw Bridge（`127.0.0.1:9481`） |
| 职责 | 店铺账号的 CRUD、授权、标签等**管理操作** | 控制已打开的浏览器实例：列出、打开、关闭 |
| 前置条件 | 只需 apiKey + 网络 | 紫鸟浏览器客户端必须已启动 |
| 典型场景 | 创建店铺、批量授权员工、管理标签 | 打开店铺浏览器 → 导航 → 截图 → 自动化操作 |

**简单记忆：** `account` = 管理后台增删改查，`store` = 控制本地浏览器窗口。

## 命令优先级

AI Agent 调用时按优先级选择：

1. **快捷命令** -- `staff list`、`department create`、`store open` 等（参数简化，体验最好）
2. **通用 api 命令** -- `api <path>` 兜底（任意接口都能调，需要手写 JSON body）
3. **zclaw invoke** -- `zclaw invoke <tool>` 兜底（任意 ZClaw 工具都能调）

## 输出格式

所有命令支持 `--format json|table|csv` 和 `--jq` 过滤：

```bash
ziniao-cli staff list --format table
ziniao-cli staff list --jq '.[].name'
ziniao-cli department list --format csv
```

### 输出结构

成功（stdout）：
```json
{"ok": true, "data": ..., "meta": {"count": 10}}
```

失败（stderr）：
```json
{"ok": false, "error": {"type": "gateway|business|auth|validation", "code": 1001, "message": "...", "hint": "..."}}
```

### 错误类型与处理

| 错误类型 | 含义 | AI Agent 应该做什么 |
|---------|------|-------------------|
| `auth` | apiKey 缺失或无效 | 提示用户运行 `ziniao-cli config init` |
| `gateway` | 网关层错误 (code != "0") | 报告错误，检查网络/apiKey |
| `business` | 业务层错误 (ret != 0) | 报告错误信息，根据 msg 判断原因 |
| `validation` | 参数校验失败 | 检查命令参数是否正确 |
| `network` | 网络不通/Bridge 未启动 | ZClaw 相关：提示启动紫鸟浏览器；API 相关：检查网络 |

## 更新检查

ziniao-cli 命令执行后，如果检测到新版本，JSON 输出中会包含 `_notice.update` 字段：

```json
{
  "ok": true,
  "data": ...,
  "_notice": {
    "update": {
      "current": "1.0.0",
      "latest": "1.1.0",
      "message": "ziniao-cli 1.1.0 可用，当前 1.0.0，运行 npm update -g @ziniao-open/cli 更新"
    }
  }
}
```

**当你在输出中看到 `_notice.update` 时：**
1. 先完成用户当前请求
2. 然后将 `message` 字段内容展示给用户，提议帮其更新
3. 若用户同意，执行 `npm update -g @ziniao-open/cli`

更新提示仅通过 stdout JSON 的 `_notice` 字段传递，不会输出到 stderr。可通过环境变量 `ZINIAO_CLI_NO_UPDATE_CHECK=1` 禁用检查。

## 环境兼容说明

### Windows Git Bash 路径转义问题

在 **Git Bash** 中，以 `/` 开头的字符串会被 MSYS 自动转换为 Windows 本地路径（如 `/superbrowser/...` → `C:/Program Files/Git/superbrowser/...`），导致 `api` 命令的路径参数被破坏。

**PowerShell 和 CMD 无此问题。**

**解决方式一（推荐）：写入 `.bashrc` 永久生效**

```bash
echo 'export MSYS_NO_PATHCONV=1' >> ~/.bashrc
source ~/.bashrc
```

**解决方式二：每次命令前加前缀**

```bash
MSYS_NO_PATHCONV=1 ziniao-cli api /superbrowser/rest/v1/erp/store/create \
  --data '{"storeData":[{"name":"新店铺"}]}'
```

## 安全规则

- **禁止输出完整 apiKey** 到终端明文
- **写入/删除操作前必须确认用户意图**
- `high-risk-write` 操作（department delete、staff remove）会要求交互式确认，可用 `--yes` 跳过
- 建议先用 `--dry-run` 预览危险请求

## 重要行为规则

- **ZClaw 本地接口必须通过 ziniao-cli 调用**：调用紫鸟浏览器本地接口（store/page/zclaw 命令）时，必须使用本技能体系中的 ziniao-cli 能力，不要使用 ziniao-assistant 技能自行调用 ZClaw Bridge。
- **店铺列表优先使用本地接口**：如果用户要求获取店铺列表，应优先使用 `store list` 快捷命令（走本地 ZClaw Bridge），因为普通成员没有服务端 `account list` 接口权限。成员类型可通过 `ziniao-cli config show` 结果中的 `isBoss` 字段判断。
- **ZClaw 认证失败排查**：如果帮用户初始化应用（`config init`）之后，请求 ZClaw 接口仍返回 API Key 认证失败，应提醒用户前往紫鸟开放平台 https://open.ziniao.com 查看自己的用户应用里「终端管理」是否已绑定当前终端识别码（识别码在紫鸟浏览器设置中查看）。
