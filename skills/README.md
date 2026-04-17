# Boohee CLI Skills

Agent skills for the Boohee CLI. Open the relevant skill file when the user's
request involves authentication, health data, or Boohee API operations.

## Available Skills

### boohee-shared

Shared foundation: auth prerequisites, output formats, scope system, and
AI-agent login flow.

Open: [boohee-shared/SKILL.md](boohee-shared/SKILL.md)

Use when:
- First-time setup or configuration questions
- Permission denied / token expired errors
- Questions about `--format` flag or output formats
- Questions about OAuth scopes

### boohee-auth

Manage Boohee authentication: login, status, logout.

Open: [boohee-auth/SKILL.md](boohee-auth/SKILL.md)

Use when:
- User says "登录"、"授权"、"login"、"authenticate"
- User asks to check auth status
- User says "登出"、"logout"、"退出登录"
- Token expired and re-authentication is needed
- AI agent needs to perform non-interactive login

### boohee-weight

Manage weight records: query history, get latest, create new records.

Open: [boohee-weight/SKILL.md](boohee-weight/SKILL.md)

Use when:
- User says "体重"、"BMI"、"体脂"、"weight"
- User asks for latest or recent weight data
- User wants to log / record a new weight
- User asks for weight history or trends

## Adding a New Skill

1. Create `skills/<skill-name>/SKILL.md` with frontmatter (`name`, `description`)
2. Add a `references/` subdirectory for detailed field or API references
3. Add an entry to this `README.md` with trigger guidance
4. Link the skill from `CLAUDE.md`'s Skill System section
