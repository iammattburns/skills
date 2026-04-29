# Personal agent skills

One **Claude Code plugin** in a flat repo: **catalog** at `.claude-plugin/marketplace.json`, **plugin** manifest at `.claude-plugin/plugin.json`, and skills under `skills/`. Layout matches [plugin directory structure](https://code.claude.com/en/plugins-reference#plugin-directory-structure) (only manifests inside `.claude-plugin/`; `skills/` lives at repo root next to it).

## Layout

| Path                              | Role                                                                                                                                                   |
| --------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `.claude-plugin/marketplace.json` | **Catalog:** `name`, required `owner: { "name" }`, `plugins[]` with `name`, `source` (e.g. `"./"`), `description`. Not the same file as `plugin.json`. |
| `.claude-plugin/plugin.json`      | **Plugin:** `name`, `version`, `description`, `author`, `keywords`, … Install id is the plugin `name` (here: **`skills`**).                            |
| `skills/<skill-name>/SKILL.md`    | One folder per skill; each `SKILL.md` uses YAML frontmatter (`name`, `description`, …).                                                                |

Optional later: `agents/`, `commands/`, `hooks/`, `.mcp.json`, etc. at the same level as `skills/` (still plugin root, not inside `.claude-plugin/`).

---

## Cursor

Copy or symlink a skill folder into personal or project skills:

- Personal: `~/.cursor/skills/<skill-name>/`
- Project: `.cursor/skills/<skill-name>/`

Example: this repo’s git skill → `skills/git-branch/`. See [Cursor skills](https://cursor.com/docs).

---

## Claude Code

### If this repo has **no** `marketplace.json`

`claude plugin marketplace add iammattburns/skills` expects a catalog at `.claude-plugin/marketplace.json`. Without it, add a **minimal** catalog in the same repo so the repo stays “one plugin, flat skills,” but still registers as a marketplace:

```json
{
  "name": "your-marketplace-id",
  "owner": { "name": "Your Name" },
  "plugins": [
    {
      "name": "skills",
      "description": "Personal skills",
      "source": "./"
    }
  ]
}
```

Save as `.claude-plugin/marketplace.json`. Use a kebab-case `name` that is not [reserved](https://code.claude.com/docs/en/plugin-marketplaces#marketplace-schema). The plugin `name` must match `"name"` in `plugin.json` (`skills`).

Then:

```text
/plugin marketplace add your-user/your-repo
/plugin install skills@your-marketplace-id
/reload-plugins
```

Use the **top-level** `name` from `marketplace.json` after `@` (this repo uses **`iammattburns-skills`**—change it in one place if you rename the marketplace).

CLI equivalents: `claude plugin marketplace add …`, `claude plugin install skills@your-marketplace-id`.

Pin a branch: `your-user/your-repo@main`. Refresh after pushes: `/plugin marketplace update iammattburns-skills` (or whatever your catalog `name` is).

### Local checkout

```text
/plugin marketplace add /absolute/path/to/this/repo
```

(Requires the same `.claude-plugin/marketplace.json` if your Claude version insists on a catalog file—see above.)

---

## Adding a skill

1. Create `skills/<skill-name>/SKILL.md` with frontmatter (`name`, `description`, …) and the body instructions.
2. Commit and push; bump `version` in `plugin.json` when you want installs to pick up a new release explicitly, or rely on git SHA behavior per [version management](https://code.claude.com/en/plugins-reference#version-management).

---

## References

- [Plugins reference](https://code.claude.com/en/plugins-reference) — `plugin.json`, `skills/`, discovery rules
- [Plugin marketplaces](https://code.claude.com/docs/en/plugin-marketplaces) — `marketplace.json`, `source`, installs
- [Discover and install plugins](https://code.claude.com/docs/en/discover-plugins) — `/plugin`, scopes, reload
