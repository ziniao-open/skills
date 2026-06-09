---
name: ziniao-access-policy
version: 1.0.0
description: "紫鸟访问策略管理：网页访问规则、网页/网页分组、规则生效成员/账号、账号绑定策略的查询和维护。当前无快捷命令，全部通过 ziniao-cli api 直调。"
metadata:
  requires:
    bins: ["ziniao-cli"]
---

# 访问策略管理

**CRITICAL — 开始前 MUST 先用 Read 工具读取 [`../ziniao-shared/SKILL.md`](../ziniao-shared/SKILL.md)**

访问策略接口用于管理 ERP 网页访问权限，包括访问规则、网页配置、网页分组，以及账号与访问策略的绑定关系。

当前没有 `access-policy` 快捷命令，全部使用通用 `api` 命令直调。所有接口均为 `POST`、`application/json`，请求体需要显式传 `companyId`。

## 关键枚举

| 字段 | 值 | 说明 |
|------|----|------|
| `activeUser` | `1` | 除 boss 外所有成员 |
| `activeUser` | `2` | 指定成员 |
| `activeUser` | `3` | 指定部门 |
| `activeUser` | `4` | 指定角色 |
| `activeAccount` | `0` | 所有账号 |
| `activeAccount` | `1` | 指定账号 |
| `activeRange` | `1` | 所有网站 |
| `activeRange` | `2` | 指定网址-黑名单 |
| `activeRange` | `3` | 指定网址-白名单 |
| `isActive` | `0` | 禁用 |
| `isActive` | `1` | 启用 |

## 访问规则

| 操作 | API |
|------|-----|
| 规则列表 | `/superbrowser/rest/v1/erp/security/access_rule/list` |
| 规则详情 | `/superbrowser/rest/v1/erp/security/access_rule/detail` |
| 新建规则 | `/superbrowser/rest/v1/erp/security/access_rule/add` |
| 编辑规则 | `/superbrowser/rest/v1/erp/security/access_rule/edit` |
| 删除规则 | `/superbrowser/rest/v1/erp/security/access_rule/delete` |
| 启用/禁用规则 | `/superbrowser/rest/v1/erp/security/access_rule/active` |
| 设置生效成员 | `/superbrowser/rest/v1/erp/security/access_rule/change_user` |
| 设置生效账号 | `/superbrowser/rest/v1/erp/security/access_rule/change_account` |
| 规则可绑定账号列表 | `/superbrowser/rest/v1/erp/security/access_rule/account/bindable_list` |

```bash
# 查询访问规则
ziniao-cli api /superbrowser/rest/v1/erp/security/access_rule/list \
  --data '{"companyId":123,"page":1,"limit":20}'

# 查看规则详情
ziniao-cli api /superbrowser/rest/v1/erp/security/access_rule/detail \
  --data '{"companyId":123,"ruleId":456}'

# 删除规则，ruleIds 是数组
ziniao-cli api /superbrowser/rest/v1/erp/security/access_rule/delete \
  --data '{"companyId":123,"ruleIds":[456]}'

# 启用规则，禁用时 isActive 传 "0"
ziniao-cli api /superbrowser/rest/v1/erp/security/access_rule/active \
  --data '{"companyId":123,"ruleIds":[456],"isActive":"1"}'
```

设置生效成员接口无快捷命令，需通过 `api` 直调。请求体字段必须按接口文档发送：

| 字段 | 类型 | 说明 |
|------|------|------|
| `ruleId` | number | 规则 ID |
| `activeUser` | number | `1` 除 boss 外所有成员；`2` 指定成员；`3` 指定部门；`4` 指定角色 |
| `userChange.addIds` | string[] | 新增成员 ID 列表；无新增传 `[]` |
| `userChange.addNames` | string[] | 新增成员用户名列表；无新增传 `[]` |
| `userChange.deleteIds` | string[] | 删除成员 ID 列表；无删除传 `[]` |
| `userChange.deleteNames` | string[] | 删除成员用户名列表；无删除传 `[]` |
| `departmentIds` | number[] | 指定部门列表；非部门模式也传 `[]` |
| `roleIds` | number[] | 指定角色列表；非角色模式也传 `[]` |

`addIds` 和 `addNames` 可任选一个传，两者都传时取并集；`deleteIds/deleteNames` 不能与新增列表重复。`companyId` 由 CLI 自动注入，不要在 `--data` 中手动传入公共参数。

```bash
ziniao-cli api /superbrowser/rest/v1/erp/security/access_rule/change_user \
  --data '{"companyId":123,"ruleId":456,"activeUser":2,"userChange":{"addIds":["1001"],"deleteIds":[],"addNames":[],"deleteNames":[]},"departmentIds":[],"roleIds":[]}'
```

设置生效账号时，`accountChange.addIds` 和 `accountChange.deleteIds` 传账号 ID 字符串数组。

```bash
ziniao-cli api /superbrowser/rest/v1/erp/security/access_rule/change_account \
  --data '{"companyId":123,"ruleId":456,"activeAccount":1,"accountChange":{"addIds":["2001"],"deleteIds":[]}}'
```

新建/编辑规则的请求体较复杂，核心结构如下：

```json
{
  "companyId": 123,
  "name": "限制文件下载",
  "desc": "运营角色适用",
  "activeUser": 2,
  "userChange": { "addIds": ["1001"], "addNames": [] },
  "departmentIds": [],
  "roleIds": [],
  "activeAccount": 0,
  "accountChange": { "addIds": [] },
  "activeRange": "3",
  "configs": [
    {
      "urlId": 789,
      "isAccessible": true,
      "isLog": true,
      "functions": [
        { "function": "file_download", "permission": 3 }
      ]
    }
  ],
  "isSkipRuleConflictCheck": false,
  "effectiveTime": { "timeType": "0", "startTime": 0, "endTime": 0 }
}
```

## 网页与网页分组

| 操作 | API |
|------|-----|
| 网页列表 | `/superbrowser/rest/v1/erp/security/access_rule_url/list` |
| 网页详情 | `/superbrowser/rest/v1/erp/security/access_rule_url/detail` |
| 添加网页 | `/superbrowser/rest/v1/erp/security/access_rule_url/add` |
| 编辑网页 | `/superbrowser/rest/v1/erp/security/access_rule_url/edit` |
| 删除网页 | `/superbrowser/rest/v1/erp/security/access_rule_url/delete` |
| 网页修改分组 | `/superbrowser/rest/v1/erp/security/access_rule_url/change_group` |
| 网页分组列表 | `/superbrowser/rest/v1/erp/security/access_rule_url_group/list` |
| 添加网页分组 | `/superbrowser/rest/v1/erp/security/access_rule_url_group/add` |
| 编辑网页分组 | `/superbrowser/rest/v1/erp/security/access_rule_url_group/edit` |
| 删除网页分组 | `/superbrowser/rest/v1/erp/security/access_rule_url_group/delete` |

```bash
# 查询网页列表
ziniao-cli api /superbrowser/rest/v1/erp/security/access_rule_url/list \
  --data '{"companyId":123,"page":1,"limit":20}'

# 添加网页
ziniao-cli api /superbrowser/rest/v1/erp/security/access_rule_url/add \
  --data '{"companyId":123,"name":"Amazon后台","groupId":10,"urlPattern":"https://sellercentral.amazon.com/*","desc":"亚马逊后台"}'

# 移动网页到分组，urlIds 是数组
ziniao-cli api /superbrowser/rest/v1/erp/security/access_rule_url/change_group \
  --data '{"companyId":123,"groupId":10,"urlIds":[789]}'

# 查询分组树，isAll=true 时返回 children
ziniao-cli api /superbrowser/rest/v1/erp/security/access_rule_url_group/list \
  --data '{"companyId":123,"groupIdParent":0,"isAll":true}'
```

## 账号绑定策略

| 操作 | API |
|------|-----|
| 账号绑定的访问策略列表 | `/superbrowser/rest/v1/erp/security/access_rule/list/one_account` |
| 账号添加访问策略 | `/superbrowser/rest/v1/erp/security/access_rule/account_ref/add_to_account` |
| 账号移除访问策略 | `/superbrowser/rest/v1/erp/security/access_rule/account_ref/remove_from_account` |

```bash
# 查询某账号已绑定的策略
ziniao-cli api /superbrowser/rest/v1/erp/security/access_rule/list/one_account \
  --data '{"companyId":123,"accountId":"2001","page":1,"limit":20}'

# 给账号添加访问策略
ziniao-cli api /superbrowser/rest/v1/erp/security/access_rule/account_ref/add_to_account \
  --data '{"companyId":123,"accountId":"2001","ruleIds":[456],"isSkipRuleConflictCheck":false}'

# 从账号移除访问策略
ziniao-cli api /superbrowser/rest/v1/erp/security/access_rule/account_ref/remove_from_account \
  --data '{"companyId":123,"accountId":"2001","ruleIds":[456]}'
```

## 注意事项

- `ruleIds`、`urlIds`、`platformIds`、`tagIds`、`departmentIds`、`roleIds` 都是数组，单个值也要包装成数组。
- 成员相关 `addIds/deleteIds/addNames/deleteNames` 是字符串数组；账号相关 `accountChange.addIds/deleteIds` 也是字符串数组。
- 新建规则文档说明 `userChange.addIds` 和 `userChange.addNames` 可任选一个传，两者都传时取并集。
- `configs.functions.permission` 常用值：`1` 允许、`2` 允许并记录、`3` 限制并记录、`6` 限制。`password`、`mouse_click`、`keyboard_input` 只支持 `1`、`6`。
- `request_id` 会由 CLI 输出到 `meta.requestId`，排查接口问题时优先记录该值。
