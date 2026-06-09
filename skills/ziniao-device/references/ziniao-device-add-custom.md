# device add-custom

> **前置条件：** 先阅读 [`../ziniao-shared/SKILL.md`](../../ziniao-shared/SKILL.md)。

添加自有代理设备。CLI 将嵌套的 `proxy` 对象展平为命名 flag。

底层 API：`/superbrowser/rest/v1/erp/ip/self/add/new`

## 命令

```bash
# 基础用法
ziniao-cli device add-custom \
  --name "美国代理1" \
  --proxy-type socks5 \
  --addr "1.2.3.4" \
  --port 1080

# 带认证
ziniao-cli device add-custom \
  --name "美国代理1" \
  --proxy-type socks5 \
  --addr "1.2.3.4" \
  --port 1080 \
  --proxy-user admin \
  --proxy-pass secret

# 动态 IP
ziniao-cli device add-custom \
  --name "动态代理" \
  --proxy-type http \
  --addr "proxy.example.com" \
  --port 8080 \
  --dynamic
```

## 参数

| 参数 | 必填 | 说明 |
|------|------|------|
| `--name` | 是 | 设备名称 |
| `--proxy-type` | 是 | http \| https \| socks5 \| ssh \| ssl |
| `--addr` | 是 | 代理地址 |
| `--port` | 是 | 代理端口 |
| `--proxy-user` | 否 | 代理用户名 |
| `--proxy-pass` | 否 | 代理密码 |
| `--dynamic` | 否 | 是否动态 IP |
| `--defy-warning` | 否 | 是否无视风险导入：1=是，0=否，默认 1 |

## 参考

- [ziniao-device](../SKILL.md) — 设备管理全部命令
