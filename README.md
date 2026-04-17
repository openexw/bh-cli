# bh-cli skills mirror

这个仓库用于公开维护 `skills` 与 `bh-cli` 的使用说明。

## 同步说明

- 本仓库仅用于公开内容同步，不包含私有项目源码。
- 当前同步范围：`skills/` 目录与仓库根 `README.md`。
- 同步命令：`make sync-skills`。

## skills

当前 skills 目录结构：

```text
skills
├── boohee-auth
│   └── SKILL.md
├── boohee-shared
│   └── SKILL.md
├── boohee-weight
│   ├── references
│   │   └── weight-fields.md
│   └── SKILL.md
└── README.md
```

安装 skills：

```bash
npx skills add openexw/bh-cli -y -g
```

## bh-cli 使用

安装 CLI：

```bash
npm install -g @boohee101/bh-cli
```

查看帮助：

```bash
bh-cli --help
bh-cli auth --help
bh-cli weight --help
```

登录与授权：

```bash
bh-cli auth login
bh-cli auth status
bh-cli auth logout
```

体重命令：

```bash
bh-cli weight latest
bh-cli weight list --page 1 --page-size 50
bh-cli weight create --weight 68.5 --measure-time "2026-04-15 08:30:00"
```

授权 scope 建议：

- 查询：`weight.read`
- 写入：`weight.write`
- 推荐登录命令：`bh-cli auth login --scope "user.read weight.read weight.write"`
