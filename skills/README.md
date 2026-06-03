# APM 独立 Skills

这里存放不随 `apm init` 主 bundle 自动安装的可选 skills。它们用于特定场景，例如解释 APM、迁移旧版本、定制中文分叉。

## 可用 Skills

### apm-assist

通用 APM 助手。用于解释 APM 概念、检查安装状态、回答使用问题、辅助迁移旧版本。

不同平台只需要把同一个 `SKILL.md` 放到对应目录：

| Platform | Skill directory |
|---|---|
| Claude Code | `.claude/skills/apm-assist/` |
| Cursor | `.cursor/skills/apm-assist/` |
| GitHub Copilot | `.github/skills/apm-assist/` |
| Gemini CLI | `.gemini/skills/apm-assist/` |
| OpenCode | `.opencode/skills/apm-assist/` |
| Codex CLI | `.agents/skills/apm-assist/` |

Codex CLI 示例：

```powershell
mkdir .agents\skills\apm-assist
copy skills\apm-assist\SKILL.md .agents\skills\apm-assist\SKILL.md
```

### apm-customization

用于指导 AI 助手修改 APM 模板、构建 release、发布自定义仓库。它已经存在于本仓库中，AI 在仓库内工作时可以直接读取：

```text
skills/apm-customization/SKILL.md
```

## 维护建议

如果你维护中文分叉，优先保持这两个 skill 的中文说明与模板实际行为一致。

