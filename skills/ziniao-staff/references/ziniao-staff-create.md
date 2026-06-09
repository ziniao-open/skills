# staff create

> **前置条件：** 先阅读 [`../ziniao-shared/SKILL.md`](../../ziniao-shared/SKILL.md)。

新增员工。写入操作。原始 API 需要 20+ 字段，CLI 简化为核心 flag，并补齐服务端需要的登录日期字段。

## 命令

```bash
# 最简用法（5 个必填参数）
ziniao-cli staff create \
  --username "zhangsan" \
  --name "张三" \
  --password "Pass123!" \
  --role-id 16691047257645 \
  --department-id 15868464646076

# 指定部门 + 限制登录终端
ziniao-cli staff create \
  --username "zhangsan" \
  --name "张三" \
  --password "Pass123!" \
  --role-id 16691047257645 \
  --department-id 15868464646076 \
  --allow-clients windows,mac,web
```

## 参数

| 参数 | 必填 | 默认值 | 说明 |
|------|------|--------|------|
| `--username` | 是 | — | 登录账号 |
| `--name` | 是 | — | 姓名 |
| `--password` | 是 | — | 密码 |
| `--role-id` | 是 | — | 角色 ID（用 `ziniao-cli role list` 或 `ziniao-cli api /superbrowser/rest/v1/erp/per/role/list` 查询） |
| `--department-id` | 是 | — | 部门 ID |
| `--mobile` | 否 | 无 | 手机号 |
| `--allow-clients` | 否 | all | 允许登录终端（windows,mac,web,android,ios,linux,miniapp） |
| `--device-auth` | 否 | auto | 设备授权模式（auto\|first\|approve\|phone） |
| `--login-start-date` | 否 | 今天 | 可登录起始日期，格式 yyyy-MM-dd |
| `--login-end-date` | 否 | 2099-12-31 | 可登录结束日期，格式 yyyy-MM-dd |

## 智能默认值

以下字段使用智能默认值，用户无需指定：

| 原始 API 字段 | 默认值 | 说明 |
|--------------|--------|------|
| `authDevide` | 1 | 全设备自动永久授权 |
| `isUpdatePersonInfo` | 1 | 允许修改个人信息 |
| `isLimitLogin` | 0 | 不限制登录时间 |
| `isTwoStepVerify` | 0 | 不开启二步验证 |
| `enablePhoneLogin` | 0 | 不开启手机号登录 |
| `authClient` | 全部 "1" | 允许所有终端 |
| `loginLimitStartDate` | 今天 | 服务端需要非空有效日期 |
| `loginLimitEndDate` | 2099-12-31 | 服务端需要非空有效日期 |

## 参考

- [ziniao-staff](../SKILL.md) — 员工管理全部命令
