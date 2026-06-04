# Personal agent skills

One flat personal skills plugin with dual agent support:

- **Claude Code** uses `.claude-plugin/marketplace.json` and `.claude-plugin/plugin.json`.
- **Codex direct plugin installs** can use `.codex-plugin/plugin.json`.
- **Codex marketplace installs** use `.agents/plugins/marketplace.json`, which points at the mirrored plugin under `plugins/skills/`.
- Claude Code and direct Codex installs read `skills/`; Codex marketplace installs read `plugins/skills/skills/`.

## Layout

| Path                              | Role                                                                                                                                                   |
| --------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------ |
| `.claude-plugin/marketplace.json` | **Catalog:** `name`, required `owner: { "name" }`, `plugins[]` with `name`, `source` (e.g. `"./"`), `description`. Not the same file as `plugin.json`. |
| `.claude-plugin/plugin.json`      | **Plugin:** `name`, `version`, `description`, `author`, `keywords`, … Install id is the plugin `name` (here: **`skills`**).                            |
| `.codex-plugin/plugin.json`       | **Codex direct plugin:** manifest and UI metadata for Codex. References the root `skills/` directory.                                                  |
| `.agents/plugins/marketplace.json` | **Codex marketplace:** catalog that exposes the nested `skills` plugin to `codex plugin add`.                                                          |
| `plugins/skills/.codex-plugin/plugin.json` | **Codex marketplace plugin:** installable plugin package used when this repo is added as a Codex marketplace.                                  |
| `skills/<skill-name>/SKILL.md`    | Root skill source used by Claude Code and direct Codex plugin installs.                                                                                |
| `plugins/skills/skills/<skill-name>/SKILL.md` | Mirrored skill source used by Codex marketplace installs. Keep this in sync with `skills/`.                                               |

Optional later: `agents/`, `commands/`, `hooks/`, `.mcp.json`, etc. at the same level as `skills/` (still plugin root, not inside `.claude-plugin/`).

---

## Cursor

Copy or symlink a skill folder into personal or project skills:

- Personal: `~/.cursor/skills/<skill-name>/`
- Project: `.cursor/skills/<skill-name>/`

Example: this repo’s git skill → `skills/git-branch/`. See [Cursor skills](https://cursor.com/docs).

---

## Claude Code

**Marketplace ≠ installed plugin.** `/plugin marketplace add …` only registers the catalog (`0 plugins` until you install). You must run **`/plugin install skills@iammattburns-skills`** (plugin name from `plugin.json`, `@` + marketplace `name` from `marketplace.json`), then **`/reload-plugins`**. In Discover, pick the **skills** plugin and install it to user or project scope if you prefer the UI.

After install, skills are **namespaced** by the plugin id, e.g. **`/skills:git-branch`** (not plain `/git-branch`). See [Discover and install plugins](https://code.claude.com/docs/en/discover-plugins).

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

## Codex

Codex reads this repo as a plugin through `.codex-plugin/plugin.json`. The Codex manifest keeps
its own UI metadata but points at the same shared skills directory:

```json
{
  "skills": "./skills/"
}
```

For a marketplace install from GitHub:

```bash
codex plugin marketplace add iammattburns/skills
codex plugin add skills@iammattburns-skills
```

After pushing updates, refresh and reinstall:

```bash
codex plugin marketplace upgrade iammattburns-skills
codex plugin add skills@iammattburns-skills
```

Start a new Codex thread after reinstalling so the updated skill list is loaded.

---

## Adding a skill

1. Create `skills/<skill-name>/SKILL.md` with frontmatter (`name`, `description`, …) and the body instructions.
2. Mirror the same skill folder under `plugins/skills/skills/<skill-name>/` for Codex marketplace installs.
3. Keep `.claude-plugin/plugin.json`, `.codex-plugin/plugin.json`, and `plugins/skills/.codex-plugin/plugin.json` versions aligned when you want a visible release bump.
4. Commit and push; bump `version` in plugin manifests when you want installs to pick up a new release explicitly, or rely on git SHA behavior per [version management](https://code.claude.com/en/plugins-reference#version-management).

---

## References

- [Plugins reference](https://code.claude.com/en/plugins-reference) — `plugin.json`, `skills/`, discovery rules
- [Plugin marketplaces](https://code.claude.com/docs/en/plugin-marketplaces) — `marketplace.json`, `source`, installs
- [Discover and install plugins](https://code.claude.com/docs/en/discover-plugins) — `/plugin`, scopes, reload
