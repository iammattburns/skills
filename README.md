# Personal agent skills

One shared `skills/` tree for **Claude Code**, **Codex**, and **Cursor**. Agent-specific files are only thin manifests—do **not** duplicate `SKILL.md` content.

## Layout

| Path | Role |
|------|------|
| `skills/<skill-name>/SKILL.md` | **Single source of truth** for every skill. |
| `.claude-plugin/marketplace.json` | Claude Code marketplace catalog (`owner`, `plugins[]`, `source: "./"`). |
| `.claude-plugin/plugin.json` | Claude Code plugin metadata (`name`: **`skills`**). |
| `.codex-plugin/plugin.json` | Codex plugin metadata + UI; points at `./skills/`. |
| `.agents/plugins/marketplace.json` | Codex marketplace catalog; plugin path is `./` (same root plugin). |

Optional later at repo root (same level as `skills/`): `agents/`, `commands/`, `hooks/`, `.mcp.json`.

---

## Cursor

Copy or symlink from the shared tree:

- Personal: `~/.cursor/skills/<skill-name>/`
- Project: `.cursor/skills/<skill-name>/`

Example: `skills/git-branch/` → `~/.cursor/skills/git-branch/`. See [Cursor skills](https://cursor.com/docs).

---

## Claude Code

**Marketplace ≠ installed plugin.** Adding a marketplace only registers the catalog. Install the plugin, then reload:

```text
/plugin marketplace add iammattburns/skills
/plugin install skills@iammattburns-skills
/reload-plugins
```

Skills are namespaced by plugin id, e.g. `/skills:git-branch`.

Refresh after pushes: `/plugin marketplace update iammattburns-skills`.

Local path: `/plugin marketplace add /absolute/path/to/this/repo`.

---

## Codex

Root `.codex-plugin/plugin.json` points at the same `skills/` directory:

```json
{
  "skills": "./skills/"
}
```

Marketplace install:

```bash
codex plugin marketplace add iammattburns/skills
codex plugin add skills@iammattburns-skills
```

After pushes:

```bash
codex plugin marketplace upgrade iammattburns-skills
codex plugin add skills@iammattburns-skills
```

Start a new Codex thread after reinstalling so the skill list refreshes.

---

## Adding a skill

1. Create **only** `skills/<skill-name>/SKILL.md` (frontmatter + instructions).
2. Optionally bump `version` in `.claude-plugin/plugin.json` and `.codex-plugin/plugin.json` for a visible release.
3. Commit and push. Claude / Codex / Cursor all consume that same folder.

---

## References

- [Plugins reference](https://code.claude.com/en/plugins-reference)
- [Plugin marketplaces](https://code.claude.com/docs/en/plugin-marketplaces)
- [Discover and install plugins](https://code.claude.com/docs/en/discover-plugins)
