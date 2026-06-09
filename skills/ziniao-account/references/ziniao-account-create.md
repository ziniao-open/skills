# account create

> **前置条件：** 先阅读 [`../ziniao-shared/SKILL.md`](../../ziniao-shared/SKILL.md)。

创建店铺账号。写入操作。CLI 将嵌套的 `storeData` 数组展平为命名 flag。

## 命令

```bash
ziniao-cli account create --name "新店铺"
ziniao-cli account create --name "US Store" --site-id 123 --proxy-id 456
```

## 参数

| 参数 | 必填 | 说明 |
|------|------|------|
| `--name` | 是 | 账号名称 |
| `--site-id` | 否 | 平台 ID |
| `--proxy-id` | 否 | 代理设备 ID |
| `--group-id` | 否 | 分组 ID |
| `--remark` | 否 | 备注 |

## 底层 API（快捷命令不可用时的兜底）

```bash
ziniao-cli api /superbrowser/rest/v1/erp/store/create \
  --data '{"storeData":[{"name":"新店铺","siteId":123}]}'
```

**注意事项：**
- `storeData` 必须是**数组**，即使只创建一个店铺
- 字段名是 `name`（不是 `storeName`）
- 在 **Windows Git Bash** 中，以 `/` 开头的路径会被自动转换为本地文件路径，需加环境变量：
  ```bash
  MSYS_NO_PATHCONV=1 ziniao-cli api /superbrowser/rest/v1/erp/store/create \
    --data '{"storeData":[{"name":"新店铺"}]}'
  ```
- 在 **PowerShell** 或 **CMD** 中无此问题

## 参考

- [ziniao-account](../SKILL.md) — 账号管理全部命令
