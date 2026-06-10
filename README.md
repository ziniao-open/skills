---
markdown-sharing:
  uri: 1b49392b-906b-496e-8309-6d8c2283b15f
---

# ziniao-skills

紫鸟浏览器 AI Agent Skill 包，覆盖 ERP 管理（员工/角色/设备/标签/部门）和店铺自动化操作，适配 ziniao-cli 与 ZClaw 本地接口。

## Skill 安装

```bash
npx skills add ziniao-open/skills -y -g
```

## ziniao-cli 安装

```bash
npm install -g @ziniao-open/cli
```

安装后验证：

```bash
ziniao-cli --version
```

## 快速开始

### 1. 初始化配置

```bash
ziniao-cli config init
```

按提示选择"新建应用"或"手动输入 API Key"，完成后凭证自动存入系统 Keychain。

### 2. AI Agent 初始化流程

AI Agent 应使用非交互模式：

```bash
ziniao-cli config init --new
```

该命令会阻塞直到审核通过、被拒绝或超时（1 小时），Agent 的处理流程：

1. 后台执行 `config init --new`
2. 从 stderr 输出中提取 URL（包含 `memberAuth?cliRequestId=` 的行）
3. 将链接展示给用户，提示在浏览器中打开完成应用创建
4. 等待命令完成（审核通过/拒绝/超时）

输出示例：

```
请在浏览器中打开以下链接完成应用创建:

  https://open.ziniao.com/memberAuth?cliRequestId=a1b2c3d4-...&from=cli

⏳ 等待应用创建及审核... (按 Ctrl+C 取消)
✓ 审核已通过
✓ 企业 ID: 15393*****3459
✓ 配置已保存
```

初始化完成后，`isBoss` 字段决定账号权限范围：

| 账号类型 | 服务端 API | ZClaw Bridge（本地浏览器） |
|---------|-----------|--------------------------|
| Boss | 全部可用 | 全部可用 |
| 成员 | 不可用 | 全部可用 |

可通过 `ziniao-cli config show` 查看当前账号类型。

### 3. 验证连通性

```bash
ziniao-cli doctor
```

### 4. 开始使用

```bash
# 查看员工列表
ziniao-cli staff list --format table

# 查看部门树
ziniao-cli department list --tree

# 查看角色列表
ziniao-cli role list --format table
```

## 命令概览

### 部门管理

| 命令 | 说明 |
|------|------|
| `department list` | 查询部门列表 |
| `department create` | 新增部门 |
| `department update` | 修改部门 |
| `department delete` | 删除部门 |
| `department reorder` | 部门排序 |

### 员工管理

| 命令 | 说明 |
|------|------|
| `staff list` | 查询员工列表 |
| `staff create` | 新增员工 |
| `staff update` | 修改员工信息 |
| `staff enable` | 启用员工 |
| `staff disable` | 禁用员工 |
| `staff remove` | 删除员工 |
| `staff transfer` | 员工调岗 |

### 角色管理

| 命令 | 说明 |
|------|------|
| `role list` | 查询角色列表 |
| `role detail` | 查看角色详情 |
| `role create` | 创建角色 |
| `role update` | 修改角色 |
| `role assign` | 分配角色给员工 |
| `role staff-roles` | 查看员工已分配角色 |
| `role permissions` | 查看可用权限列表 |

### 账号（店铺）管理

| 命令 | 说明 |
|------|------|
| `account list` | 查询账号列表 |
| `account create` | 创建账号 |
| `account update` | 修改账号信息 |
| `account delete` | 删除账号 |
| `account auth-add` | 授权员工访问店铺 |
| `account auth-remove` | 撤销员工授权 |
| `account auth-clean` | 清空店铺所有授权 |
| `account auth-list` | 查询员工被授权的店铺 |
| `account auth-users` | 查询店铺已授权的员工 |
| `account tag-list` | 查询企业标签列表 |
| `account tag-create` | 创建标签 |
| `account tag-rename` | 重命名标签 |
| `account tag-delete` | 删除标签 |
| `account tag-bind` | 绑定标签到店铺 |
| `account tag-unbind` | 解绑标签 |
| `account tag-replace` | 全量替换店铺标签 |
| `account tag-clear` | 清空店铺标签 |
| `account tag-remove` | 从店铺移除标签 |
| `account tag-of-store` | 查询店铺绑定的标签 |

### 设备管理

| 命令 | 说明 |
|------|------|
| `device list` | 查询设备列表 |
| `device add-custom` | 添加自有设备 |
| `device update-custom` | 修改自有设备 |
| `device plan-list` | 查询可购买套餐 |
| `device bind` | 绑定设备到店铺 |
| `device unbind` | 解绑设备 |
| `device auto-renew` | 设置自动续费 |
| `device purchase` | 购买设备套餐 |
| `device renew` | 续费设备 |

### 浏览器控制（ZClaw）

| 命令 | 说明 |
|------|------|
| `store list` | 列出已打开的店铺浏览器 |
| `store open` | 打开店铺浏览器 |
| `store close` | 关闭店铺浏览器 |
| `page visit` | 导航到指定 URL |
| `page screenshot` | 页面截图 |
| `page automation` | 页面自动化操作 |

### 通用 API 调用

对于未提供快捷命令的接口（共 73 个接口中 shortcut 覆盖 45 个），可使用通用 `api` 命令：

```bash
ziniao-cli api <path> [--data '{}'] [--format table] [--jq '.field']
ziniao-cli api /superbrowser/rest/v1/erp/store/deletecookie --data '{"storeId":"xxx"}'
```

## Skill 模块

| Skill | 说明 |
|-------|------|
| `ziniao-shared` | 共享基础：配置初始化、认证、输出格式、权限点 |
| `ziniao-staff` | 员工管理 |
| `ziniao-role` | 角色与权限管理 |
| `ziniao-department` | 部门管理 |
| `ziniao-account` | 账号（店铺）管理与标签 |
| `ziniao-device` | 设备管理 |
| `ziniao-store` | 浏览器店铺控制（ZClaw） |
| `ziniao-page` | 浏览器页面操作（ZClaw） |
| `ziniao-access-policy` | 访问策略管理 |
| `ziniao-openapi-explorer` | API 路径清单与探索指引 |
| `ziniao-workflow-batch-account` | 批量账号授权工作流 |
| `ziniao-workflow-store-patrol` | 店铺巡检工作流 |
| `ziniao-skill-maker` | 自定义 Skill 创建指引 |

## 适用环境

这些 Skill 文档适用于任何支持工具调用的 AI Agent 环境：

- Claude Code
- Cursor
- OpenClaw
- 其他支持 Skill 协议的 Agent

## License

CC BY 4.0
