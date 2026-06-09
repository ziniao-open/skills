---
name: ziniao-device
version: 1.1.0
description: "紫鸟设备管理：代理设备（IP）的查询、添加、修改、绑定/解绑、购买续费和自动续费。当用户需要查看代理设备列表、添加/修改自有代理、绑定设备到店铺或管理设备费用时使用。"
metadata:
  requires:
    bins: ["ziniao-cli"]
---

# 设备管理

**CRITICAL — 开始前 MUST 先用 Read 工具读取 [`../ziniao-shared/SKILL.md`](../ziniao-shared/SKILL.md)**

管理紫鸟浏览器使用的代理设备（IP）。常用 9 个接口已有快捷命令覆盖；价格查询、历史绑定记录使用通用 `api` 命令。

## 代理类型映射

| CLI 值 | API 值 | 说明 |
|--------|--------|------|
| http | 0 | HTTP 代理 |
| https | 1 | HTTPS 代理 |
| socks5 | 2 | SOCKS5 代理 |
| ssh | 3 | SSH 隧道 |
| ssl | 4 | SSL 代理 |

## Shortcuts — 查询与自有设备

| 命令 | 说明 | 详细文档 |
|------|------|---------|
| `device list` | 查询设备列表 | [`references/ziniao-device-list.md`](references/ziniao-device-list.md) |
| `device add-custom` | 添加自有设备 | [`references/ziniao-device-add-custom.md`](references/ziniao-device-add-custom.md) |
| `device update-custom` | 修改自有设备 | 部分更新：`--id xxx --addr "1.2.3.4"` |

```bash
ziniao-cli device list --format table
ziniao-cli device list --page-all

ziniao-cli device add-custom --name "我的代理" --proxy-type socks5 \
  --addr "1.2.3.4" --port 1080 --proxy-user admin --proxy-pass secret

ziniao-cli device update-custom --id 123 --addr "5.6.7.8"
```

底层接口：设备列表 `/superbrowser/rest/v1/erp/ip/page`；添加自有设备 `/superbrowser/rest/v1/erp/ip/self/add/new`；修改自有设备 `/superbrowser/rest/v1/erp/ip/self/edit/new`。

## Shortcuts — 绑定管理

| 命令 | 说明 |
|------|------|
| `device bind --id --store-id` | 将设备绑定到店铺（`--store-id` 支持批量） |
| `device unbind --store-id` | 按店铺 ID 解绑设备（`--store-id` 支持批量） |

```bash
# 将设备 10 绑定到三个店铺
ziniao-cli device bind --id 10 --store-id "101,102,103"

# 批量解绑
ziniao-cli device unbind --store-id "101,102,103"
```

> ⚠ 批量解绑为逐个执行，部分失败不影响已成功项，也不会回滚。

底层接口：绑定 `/superbrowser/rest/v1/erp/ip/bind`；解绑 `/superbrowser/rest/v1/erp/ip/unbind`。

## Shortcuts — 费用相关（涉及真实扣费）

> ⚠ `purchase` 和 `renew` 为 `high-risk-write`，执行前会要求确认或需传 `--yes`。

| 命令 | 说明 |
|------|------|
| `device plan-list` | 查询可购买的套餐列表（先看这里） |
| `device purchase --plan-id --period-id --count` | 购买套餐（涉及费用，不可撤销） |
| `device renew --id --period-id` | 续费设备（`--id` 支持批量，涉及费用） |
| `device auto-renew --id --enable` | 批量开启/关闭自动续费 |

```bash
# 先查套餐，再购买
ziniao-cli device plan-list --format table
ziniao-cli device purchase --plan-id "plan_xxx" --period-id 1 --count 3

# 续费单台
ziniao-cli device renew --id 10 --period-id 1

# 批量开启自动续费
ziniao-cli device auto-renew --id "10,11,12" --enable true

# 关闭自动续费（bool 类型必须用等号连接）
ziniao-cli device auto-renew --id "10,11,12" --enable=false

# 关闭自动续费
ziniao-cli device auto-renew --id 10 --enable=false
```

底层接口：套餐 `/superbrowser/rest/v1/erp/package/new`；购买 `/superbrowser/rest/v1/erp/purchase`；续费 `/superbrowser/rest/v1/erp/renew`；自动续费 `/superbrowser/rest/v1/erp/ip/renewal`。

## 通用 api 覆盖

```bash
# 查询已购设备价格
ziniao-cli api /superbrowser/rest/v1/erp/proxy/purchased_package_info --data '{"proxyId":"123"}'

# 查询设备历史绑定记录
ziniao-cli api /superbrowser/rest/v1/erp/ip/historybind --data '{"proxyId":"123"}'
```
