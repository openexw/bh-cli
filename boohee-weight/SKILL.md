---
name: boohee-weight
description: 管理体重记录：获取最新体重、查询历史列表、新增体重记录。所需 scope：weight.read（查询）、weight.write（新增）。
---

# Boohee Weight — 体重管理

共享基础参考：[boohee-shared/SKILL.md](../boohee-shared/SKILL.md)  
字段完整说明：[references/weight-fields.md](references/weight-fields.md)

## 触发场景

- 用户说"体重"、"weight"、"BMI"、"体脂"、"肌肉量"
- 用户说"最新体重"、"最近体重"、"今天的体重"
- 用户说"查看体重历史"、"最近一周的体重"
- 用户说"记录体重"、"新增体重"、"我今天体重 xx kg"

所需 scope：
- 查询：`weight.read`
- 新增：`weight.write`

## 子命令

### `weight latest` — 获取最新体重

```
boohee-cli weight latest [--format <format>]
```

返回最近一次测量记录。

```bash
boohee-cli weight latest
boohee-cli weight latest --format pretty
```

输出示例（JSON）：
```json
{
  "measure_time": "2026-04-14 08:30:00",
  "weight": 68.5,
  "bmi": 22.3,
  "body_fat": 18.5,
  "muscle": 52.1,
  "created_at": "2026-04-14T08:30:00Z"
}
```

无记录时：输出提示信息到 stderr，无 stdout 输出。

---

### `weight list` — 查询体重历史

```
boohee-cli weight list [flags]
```

| Flag | 说明 | 默认值 |
|------|------|--------|
| `--start-date` | 开始日期，格式 `YYYY-MM-DD` | — |
| `--end-date` | 结束日期，格式 `YYYY-MM-DD` | — |
| `--page` | 页码 | 1 |
| `--page-size` | 每页记录数（最大 100） | 20 |
| `--format` | 输出格式 | json |

```bash
# 查询最近 7 天
boohee-cli weight list --start-date 2026-04-08 --end-date 2026-04-15

# 查询全部，每页 50 条
boohee-cli weight list --page-size 50

# 表格格式查看
boohee-cli weight list --start-date 2026-04-01 --format table
```

输出示例（JSON）：
```json
{
  "items": [
    {
      "measure_time": "2026-04-14 08:30:00",
      "weight": 68.5,
      "bmi": 22.3,
      "body_fat": 18.5
    }
  ],
  "total": 1,
  "page": 1,
  "page_size": 20
}
```

---

### `weight create` — 新增体重记录

```
boohee-cli weight create --weight <kg> --measure-time <datetime> [flags]
```

| Flag | 说明 | 必填 |
|------|------|------|
| `--weight` | 体重（kg） | 是 |
| `--measure-time` | 测量时间，格式 `"2006-01-02 15:04:05"` | 是 |
| `--bmi` | 体质指数 | 否 |
| `--body-fat` | 体脂率（%） | 否 |
| `--muscle` | 肌肉量（kg） | 否 |
| `--bone` | 骨量（kg） | 否 |
| `--water` | 体水分率（%） | 否 |
| `--bmr` | 基础代谢率（kcal） | 否 |
| `--visceral-fat` | 内脏脂肪等级（整数） | 否 |
| `--device` | 来源设备标识 | 否 |

```bash
# 仅记录体重
boohee-cli weight create --weight 68.5 --measure-time "2026-04-15 08:30:00"

# 记录体重 + 体脂 + BMI
boohee-cli weight create \
  --weight 68.5 \
  --measure-time "2026-04-15 08:30:00" \
  --bmi 22.3 \
  --body-fat 18.5
```

成功后输出 success 提示到 stderr，无 stdout 输出。

---

## 编排示例

**查询最近 7 天体重趋势**：
```bash
START=$(date -v-7d +%Y-%m-%d)   # macOS
END=$(date +%Y-%m-%d)
boohee-cli weight list --start-date "$START" --end-date "$END" --format json \
  | jq '.items[] | {date: .measure_time, weight: .weight}'
```

**获取最新体重并分析**：
```bash
boohee-cli weight latest --format json | jq '{
  weight,
  bmi,
  body_fat,
  健康评估: (if .bmi < 18.5 then "偏轻" elif .bmi < 24 then "正常" elif .bmi < 28 then "偏重" else "肥胖" end)
}'
```

**批量导出为 CSV**：
```bash
boohee-cli weight list --page-size 100 --format csv > weight_export.csv
```
