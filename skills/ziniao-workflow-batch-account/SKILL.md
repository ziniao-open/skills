---
name: ziniao-workflow-batch-account
version: 1.0.0
description: "批量账号授权工作流：编排 account list + staff list + api 调用完成批量授权店铺给员工。适用于新员工入职时批量分配店铺权限。"
metadata:
  requires:
    bins: ["ziniao-cli"]
---

# 批量账号授权工作流

**CRITICAL — 开始前 MUST 先用 Read 工具读取 [`../ziniao-shared/SKILL.md`](../ziniao-shared/SKILL.md)**

## 适用场景

- "给新员工分配店铺权限"
- "批量授权店铺给某个员工"
- "新人入职，需要授权所有店铺"

## 前置条件

- 已配置 apiKey（`ziniao-cli config init`）

## 工作流

```
staff list ──► 找到目标员工 ID
account list ──► 找到目标店铺 ID 列表
    │
    ├──► 对每个店铺:
    │       api /superbrowser/rest/v1/erp/store/auth/add { storeIdList, staffId }
    │
    └──► 汇总授权结果
```

### Step 1: 查找员工

```bash
# 按姓名查找
ziniao-cli staff list --name "张三" --format table
# 记录 userId
```

### Step 2: 查找要授权的店铺

```bash
# 列出所有店铺
ziniao-cli account list --format table --page-all
# 或按名称搜索
ziniao-cli account list --name "US" --format table
```

### Step 3: 逐个授权

对每个店铺执行：

```bash
ziniao-cli api /superbrowser/rest/v1/erp/store/auth/add \
  --data '{"storeIdList":["<storeId>"],"staffId":"<staffId>"}'
```

### Step 4: 验证

```bash
# 查看该员工已授权的店铺
ziniao-cli api /superbrowser/rest/v1/erp/user/stores \
  --data '{"userId":"<userId>","page":1,"limit":10}'
```

## 注意事项

- 授权是写入操作，执行前确认用户意图
- 如果需要授权大量店铺，建议先列出计划并确认
- 授权失败时记录错误并继续下一个

## 参考

- [ziniao-account](../ziniao-account/SKILL.md) — 账号管理
- [ziniao-staff](../ziniao-staff/SKILL.md) — 员工管理
- [ziniao-shared](../ziniao-shared/SKILL.md) — 认证和全局参数
