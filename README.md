# Personal agent skills

One shared `skills/` tree for **Claude Code**, **Codex**, and **Cursor**. Agent-specific files are only thin manifests—do **not** duplicate `SKILL.md` content.

## Layout

| Path | Role |
|------|------|
| `skills/<skill-name>/SKILL.md` | **Single source of truth** for every skill. |
| `.cursor-plugin/plugin.json` | Cursor plugin manifest ([creating plugins](https://cursor.com/docs/plugins#creating-plugins)). Discovers `skills/`. |
| `.claude-plugin/marketplace.json` | Claude Code marketplace catalog (`owner`, `plugins[]`, `source: "./"`). |
| `.claude-plugin/plugin.json` | Claude Code plugin metadata (`name`: **`skills`**). |
| `.codex-plugin/plugin.json` | Codex plugin metadata + UI; points at `./skills/`. |
| `.agents/plugins/marketplace.json` | Codex marketplace catalog; plugin path is `./` (same root plugin). |
| `assets/` | Shared logos / icons for plugin UIs. |

Optional later at repo root (same level as `skills/`): `rules/`, `agents/`, `commands/`, `hooks/`, `mcp.json`.

---

## Cursor

This repo is a [Cursor plugin](https://cursor.com/docs/plugins#creating-plugins): `.cursor-plugin/plugin.json` plus the shared `skills/` folder.

### Local install (dev)

Symlink the repo into Cursor’s local plugins folder, then reload:

```bash
ln -s /Users/mattburns/Development/projects-personal/skills/skills ~/.cursor/plugins/local/skills
```

Then **Developer: Reload Window** (or restart Cursor). Skills should appear under **Customize**; invoke with `/skill-name` (for example `/git-branch`).

### Per-skill fallback

You can still copy or symlink individual folders without the plugin:

- Personal: `~/.cursor/skills/<skill-name>/`
- Project: `.cursor/skills/<skill-name>/`

### Publish / team marketplace

- Public marketplace: [cursor.com/marketplace/publish](https://cursor.com/marketplace/publish)
- Team marketplace: **Dashboard → Plugins → Add Marketplace** and import this GitHub repo ([plugins docs](https://cursor.com/docs/plugins))

For multi-plugin repos you would add `.cursor-plugin/marketplace.json`; this repo is a **single** root plugin, so only `plugin.json` is required. See the [plugins reference](https://cursor.com/docs/reference/plugins).

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
2. Optionally bump `version` in `.cursor-plugin/plugin.json`, `.claude-plugin/plugin.json`, and `.codex-plugin/plugin.json` for a visible release.
3. Commit and push. Claude / Codex / Cursor all consume that same folder.

---

## References

- [Cursor plugins](https://cursor.com/docs/plugins) / [plugins reference](https://cursor.com/docs/reference/plugins)
- [Claude plugins reference](https://code.claude.com/en/plugins-reference)
- [Claude plugin marketplaces](https://code.claude.com/docs/en/plugin-marketplaces)
- [Discover and install plugins](https://code.claude.com/docs/en/discover-plugins)
