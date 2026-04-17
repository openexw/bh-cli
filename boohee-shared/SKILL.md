---
name: boohee-shared
description: Boohee CLI 共享基础：安装检测与引导、认证前置条件、输出格式、Scope 体系、AI Agent 登录流程。所有其他 boohee-* skill 的基础参考。
---

# Boohee CLI 共享基础

## 安装检测（每次执行命令前必须先做）

在执行任何 `bh-cli` 命令前，先检查 CLI 是否可用：

```bash
bh-cli --version
```

### CLI 未安装

如果命令不存在（`command not found`），告知用户并引导安装：

> `bh-cli` 尚未安装，请运行以下命令安装：
>
> ```bash
> npm install -g @boohee101/bh-cli
> ```
>
> 安装完成后重试。

安装后重新执行原命令。

### Skills 未安装

如果 AI 无法读取到 boohee-* skill（如本文件），说明 skill 尚未添加，引导用户安装：

> Boohee skills 尚未安装，请运行以下命令添加：
>
> ```bash
> npx skills add openexw/bh-cli -y -g
> ```
>
> 安装完成后重试。

## 认证前置

所有获取或写入健康数据的命令都需要有效的 OAuth token。  
Token 存储在 `~/.bh-cli/token.json`，配置存储在 `~/.bh-cli/config.json`。

在执行数据命令之前，先通过 `auth status` 检查认证状态：

```bash
bh-cli auth status
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
bh-cli auth login --scope "weight.read weight.write"
```

## AI Agent 登录流程（Device Flow 两阶段）

AI Agent 无法操作浏览器，需使用两阶段登录：

**Step 1：发起授权，获取 device_code**
```bash
bh-cli auth login --no-wait --format json --scope "weight.read weight.write"
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
bh-cli auth login --device-code abc123...
```

成功后 token 自动保存，后续命令无需重新登录。

## 常见错误处理

| 错误 | 原因 | 解决方式 |
|------|------|---------|
| `token not found` | 未登录 | 执行 `auth login` |
| `token expired` | Token 过期 | 重新执行 `auth login` |
| `permission denied` / `403` | Scope 不足 | 用正确 scope 重新登录 |
