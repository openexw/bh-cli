---
name: boohee-shared
description: Boohee CLI 共享基础：认证前置条件、输出格式、Scope 体系、AI Agent 登录流程。所有其他 boohee-* skill 的基础参考。
---

# Boohee CLI 共享基础

## 认证前置

所有获取或写入健康数据的命令都需要有效的 OAuth token。  
Token 存储在 `~/.boohee-cli/token.json`，配置存储在 `~/.boohee-cli/config.json`。

在执行数据命令之前，先通过 `auth status` 检查认证状态：

```bash
boohee-cli auth status
# 返回 JSON，status 字段为 "authorized" 或 "not_authorized"
```

如果未授权，参考 [boohee-auth skill](../boohee-auth/SKILL.md) 完成登录。

## 输出格式（`--format`）

所有数据命令支持 `--format` flag，可选值：

| 格式 | 说明 | 适用场景 |
|------|------|---------|
| `json` | 单个 JSON 对象（默认） | AI 解析、管道传递 |
| `pretty` | 人类可读的格式化输出 | 终端直接查看 |
| `table` | 表格形式 | 多条记录对比 |
| `jsonl` | 每行一个 JSON 对象 | 流式处理、日志 |
| `csv` | CSV 格式 | 导出到电子表格 |

AI Agent 推荐使用 `--format json`（默认），便于解析。

## Scope 体系

登录时通过 `--scope` 指定权限范围（空格分隔）：

| Scope | 说明 |
|-------|------|
| `user.read` | 读取用户基本信息 |
| `weight.read` | 读取体重数据 |
| `weight.write` | 写入体重数据 |

示例：同时获取读写体重权限：
```bash
boohee-cli auth login --scope "weight.read weight.write"
```

## AI Agent 登录流程（Device Flow 两阶段）

AI Agent 无法操作浏览器，需使用两阶段登录：

**Step 1：发起授权，获取 device_code**
```bash
boohee-cli auth login --no-wait --format json --scope "weight.read weight.write"
```

输出（JSON）：
```json
{
  "event": "device_authorization",
  "verification_uri_complete": "https://api.iboohee.com/...",
  "device_code": "abc123...",
  "user_code": "XXXX-XXXX"
}
```

**Step 2：告知用户在浏览器打开 `verification_uri_complete` 完成授权**

**Step 3：用户授权后，用 device_code 恢复轮询**
```bash
boohee-cli auth login --device-code abc123...
```

成功后 token 自动保存，后续命令无需重新登录。

## 常见错误处理

| 错误 | 原因 | 解决方式 |
|------|------|---------|
| `token not found` | 未登录 | 执行 `auth login` |
| `token expired` | Token 过期 | 重新执行 `auth login` |
| `permission denied` / `403` | Scope 不足 | 用正确 scope 重新登录 |
