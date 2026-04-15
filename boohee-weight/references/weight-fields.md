# Weight 字段参考

`WeightDetail` 的完整字段说明。

## 必填字段

| 字段 | 类型 | 含义 | 单位 |
|------|------|------|------|
| `measure_time` | string | 测量时间 | `"YYYY-MM-DD HH:MM:SS"` |
| `weight` | float64 | 体重 | kg |
| `created_at` | string | 记录创建时间（只读） | ISO 8601 |
| `updated_at` | string | 记录更新时间（只读） | ISO 8601 |

## 体成分字段（选填）

| 字段 | 类型 | 含义 | 单位 | CLI Flag |
|------|------|------|------|----------|
| `bmi` | float64 | 体质指数（Body Mass Index） | — | `--bmi` |
| `body_fat` | float64 | 体脂率 | % | `--body-fat` |
| `muscle` | float64 | 肌肉量 | kg | `--muscle` |
| `bone` | float64 | 骨量 | kg | `--bone` |
| `water` | float64 | 体水分率 | % | `--water` |
| `bmr` | float64 | 基础代谢率（Basal Metabolic Rate） | kcal/天 | `--bmr` |
| `visfat` | int | 内脏脂肪等级 | 整数（1-20） | `--visceral-fat` |
| `protein` | float64 | 蛋白质含量 | % | — |
| `subfat` | float64 | 皮下脂肪率 | % | — |
| `sinew` | float64 | 肌腱/韧带相关指标 | kg | — |
| `fat_free_weight` | float64 | 去脂体重 | kg | — |
| `skeletal_muscle_mass` | float64 | 骨骼肌量 | kg | — |
| `muscle_rate` | float64 | 肌肉率 | % | — |
| `fat_mass` | float64 | 脂肪量 | kg | — |
| `water_content` | float64 | 水分含量 | kg | — |

## 健康评估字段（选填，通常由设备/服务端计算）

| 字段 | 类型 | 含义 | 说明 |
|------|------|------|------|
| `health_score` | float64 | 健康评分 | 综合评分 |
| `obesity` | float64 | 肥胖度 | 相对于标准体重的偏差率 |
| `bodyage` | int | 体龄 | 身体年龄（岁） |
| `stature` | string | 体型分类 | 如"标准"、"偏胖"等 |
| `fat_burning_min` | int | 燃脂心率下限 | bpm |
| `fat_burning_max` | int | 燃脂心率上限 | bpm |
| `avg_bmi` | float64 | 平均 BMI | — |
| `avg_body_fat` | float64 | 平均体脂率 | % |
| `advice_text` | string | 健康建议文本 | 由服务端生成 |
| `bone_rate` | string | 骨量评级 | 文字描述 |
| `waist_hip_ratio` | float64 | 腰臀比 | — |

## 设备/元数据字段（选填）

| 字段 | 类型 | 含义 | CLI Flag |
|------|------|------|----------|
| `device` | string | 来源设备标识 | `--device` |
| `weight_source` | string | 体重来源标识 | — |
| `record_on` | string | 记录日期 | — |
| `impedance_one` | float64 | 阻抗值1（生物电阻抗分析用） | Ω |
| `impedance_two` | float64 | 阻抗值2 | Ω |
| `photo_url` | string | 体型照片 URL | — |
| `ext_info` | string | 扩展信息（JSON 字符串） | — |
| `daily_id` | int | 关联的日记 ID | — |
| `snapshot_age` | int | 记录时的年龄快照 | 岁 |
| `snapshot_height` | int | 记录时的身高快照 | cm |
| `snapshot_bmr` | float64 | 记录时的 BMR 快照 | kcal/天 |

## BMI 参考

| BMI 范围 | 分类 |
|----------|------|
| < 18.5 | 偏轻（Underweight） |
| 18.5 – 23.9 | 正常（Normal） |
| 24.0 – 27.9 | 偏重（Overweight） |
| ≥ 28.0 | 肥胖（Obese） |

> 以上分类适用于中国成年人标准（与 WHO 标准略有差异）。
