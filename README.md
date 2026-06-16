# patrek-ai-plugins

A centralized [plugin marketplace](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/plugins-marketplace)
for AI coding CLIs. Plugins live in their own repositories and are federated here
by reference: each registry entry's `source` points at the plugin's own GitHub
repository, pinned to a tag.

Two scoped registries are maintained so each tool only sees plugins it can run:

| Tool                | Registry file                     |
| ------------------- | --------------------------------- |
| GitHub Copilot CLI  | `.github/plugin/marketplace.json` |
| Claude Code         | `.claude-plugin/marketplace.json` |

## Plugins

| Plugin                | Tool        | Description                                         |
| --------------------- | ----------- | --------------------------------------------------- |
| `warp-notifications`  | Copilot CLI | Native Warp terminal notifications for Copilot CLI. |

## Use it (Copilot CLI)

```bash
copilot plugin marketplace add patrek/patrek-ai-plugins
copilot plugin install warp-notifications@patrek-ai-plugins
copilot plugin list
```

## Use it (Claude Code)

```bash
/plugin marketplace add patrek/patrek-ai-plugins
# then install a listed plugin once the Claude registry has entries
```

## Cloning

```bash
git clone https://github.com/patrek/patrek-ai-plugins.git
```

## Adding a plugin

1. Add an entry to the appropriate registry file (`.github/plugin/marketplace.json`
   for Copilot, `.claude-plugin/marketplace.json` for Claude) with a `source` object
   pointing at the plugin's own repository, pinned to a tag:

   ```json
   {
     "name": "<name>",
     "description": "...",
     "version": "<x.y.z>",
     "source": {
       "source": "github",
       "repo": "<owner>/<repo>",
       "ref": "v<x.y.z>"
     }
   }
   ```

   Add `"path": "<subdir>"` if the plugin's `plugin.json` is not at the repo root.

2. Keep the registry entry's `version` and the `ref` tag aligned with the plugin's
   own `plugin.json` version. Commit the registry change.

## Updating a plugin to a new release

Bump the `ref` (and matching `version`) in the registry file to the new tag, then
commit. The new tag must already exist in the plugin's own repository.

```json
"source": { "source": "github", "repo": "<owner>/<repo>", "ref": "v<x.y.z>" }
```
