# staff list

> **前置条件：** 先阅读 [`../ziniao-shared/SKILL.md`](../../ziniao-shared/SKILL.md)。

查询员工列表。只读操作。

## 命令

```bash
# 查询全部
ziniao-cli staff list

# 按姓名搜索（模糊）
ziniao-cli staff list --name "张三"

# 精确匹配
ziniao-cli staff list --name "张三" --exact

# 按状态筛选 + 表格输出
ziniao-cli staff list --status active --format table

# 按角色筛选
ziniao-cli staff list --role boss

# 按部门筛选
ziniao-cli staff list --department-id 15868464646076

# 自动翻页
ziniao-cli staff list --page-all --page-size 50

# jq 过滤
ziniao-cli staff list --jq '.[].name'
```

## 参数

| 参数 | 必填 | 说明 |
|------|------|------|
| `--name` | 否 | 按姓名搜索 |
| `--username` | 否 | 按账号搜索 |
| `--department-id` | 否 | 按部门 ID 筛选（逗号分隔） |
| `--status` | 否 | active \| disabled \| deleted |
| `--role` | 否 | boss \| manager \| employee \| admin |
| `--exact` | 否 | 精确匹配（默认模糊） |
| `--page-size` | 否 | 每页条数（默认 20） |
| `--page-all` | 否 | 自动翻页获取全部 |
| `--page-limit` | 否 | 最大翻页数（默认 10，0 为不限） |
| `--page-delay` | 否 | 翻页间隔毫秒数（默认 200） |
| `--format` | 否 | json \| table \| csv |
| `--jq` / `-q` | 否 | jq 过滤表达式 |

## 输出字段翻译

表格输出时，枚举值自动翻译：
- `level "0"` -> `role: "Boss"`
- `delflag "0"` -> `statusText: "正常"`

## 注意事项

- 默认返回所有状态的员工（含已禁用和已删除）。如果需要取员工 ID 做后续操作（分配角色、授权店铺等），建议加 `--status active` 筛选，避免使用已删除员工 ID 导致"无效的 staffId"错误。

## 参考

- [ziniao-staff](../SKILL.md) — 员工管理全部命令
