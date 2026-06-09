# staff enable / disable / remove

> **前置条件：** 先阅读 [`../ziniao-shared/SKILL.md`](../../ziniao-shared/SKILL.md)。

启用、禁用或删除员工。原始 API 是一个接口（`/erp/staff/status`），CLI 拆为 3 个语义化命令。

## 命令

```bash
# 启用
ziniao-cli staff enable --id 16524289555087

# 禁用（支持批量）
ziniao-cli staff disable --id 16524289555087
ziniao-cli staff disable --id "id1,id2,id3"

# 删除（高风险，需确认）
ziniao-cli staff remove --id 16524289555087
ziniao-cli staff remove --id 16524289555087 --yes  # 跳过确认
```

## 参数

| 参数 | 必填 | 说明 |
|------|------|------|
| `--id` | 是 | 员工 ID（逗号分隔可批量） |
| `--yes` | 否 | 跳过确认（仅 remove） |

## 风险等级

| 命令 | 风险等级 | 是否需确认 |
|------|---------|----------|
| `staff enable` | write | 否 |
| `staff disable` | write | 否 |
| `staff remove` | high-risk-write | 是（交互式确认） |

## 内部映射

| 命令 | API status 值 |
|------|--------------|
| enable | "0" |
| disable | "1" |
| remove | "2" |

## 参考

- [ziniao-staff](../SKILL.md) — 员工管理全部命令
