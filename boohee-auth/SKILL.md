---
name: boohee-auth
description: 管理 Boohee 认证：登录（Device Flow）、查看授权状态、登出。包含 AI Agent 两阶段登录编排。
---

# Boohee Auth — 认证管理

共享基础参考：[boohee-shared/SKILL.md](../boohee-shared/SKILL.md)

## 触发场景

- 用户说"登录"、"授权"、"login"、"authenticate"
- 用户说"查看登录状态"、"我登录了吗"、"auth status"
- 用户说"登出"、"退出登录"、"logout"、"清除 token"
- 遇到 token 过期、未授权错误需要重新登录

## 子命令

### `auth login` — 登录

```
boohee-cli auth login [flags]
```

| Flag | 说明 | 默认值 |
|------|------|--------|
| `--scope` | OAuth 权限范围，空格分隔 | 交互式选择 |
| `--no-wait` | 发起授权后立即返回（不等待用户确认） | false |
| `--device-code` | 用已有 device_code 恢复轮询 | — |
| `--client-id` | 自定义 OAuth Client ID | — |
| `--client-secret` | 自定义 OAuth Client Secret | — |
| `--format` | 输出格式：`pretty`（默认）\| `json` | pretty |

**交互式登录**（终端）：
```bash
boohee-cli auth login
# 弹出选择界面，选择授权应用和权限范围
# 打开浏览器 URL 完成授权
```

**指定 scope 登录**（跳过交互）：
```bash
boohee-cli auth login --scope "weight.read weight.write"
```

**AI Agent 两阶段登录**：
```bash
# Step 1：发起授权
boohee-cli auth login --no-wait --format json --scope "weight.read weight.write"
# 输出 device_code 和 verification_uri_complete，让用户在浏览器打开 URL

# Step 2：用户授权后恢复
boohee-cli auth login --device-code <device_code>
```

成功后输出（`--format json`）：
```json
{
  "status": "authorized",
  "token": { "access_token": "...", "scope": "weight.read weight.write" },
  "user": { "nickname": "张三", "open_id": "xxx" }
}
```

---

### `auth status` — 查看授权状态

```
boohee-cli auth status
```

输出（JSON）：
```json
{
  "client_id": "qq1kmbf7yi",
  "scope": "weight.read weight.write",
  "status": "authorized",
  "token": {
    "access_token": "...",
    "refresh_token": "...",
    "expires_at": "2026-05-15T09:00:00Z"
  }
}
```

`status` 字段：
- `"authorized"` — 已登录，有有效 token
- `"not_authorized"` — 未登录

---

### `auth logout` — 登出

```
boohee-cli auth logout
```

清除本地存储的 token（`~/.boohee-cli/token.json`）。  
不会向服务端发送撤销请求，只删除本地文件。

---

## 编排示例

**检查是否已登录，未登录则提示用户**：
```bash
STATUS=$(boohee-cli auth status | jq -r '.status')
if [ "$STATUS" != "authorized" ]; then
  echo "请先登录：boohee-cli auth login --scope 'weight.read'"
fi
```

**切换账号**：
```bash
boohee-cli auth logout
boohee-cli auth login --scope "weight.read weight.write"
```
