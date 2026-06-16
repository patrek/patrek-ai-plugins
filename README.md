# patrek-ai-plugins

A centralized [plugin marketplace](https://docs.github.com/en/copilot/how-tos/copilot-cli/customize-copilot/plugins-marketplace)
for AI coding CLIs. Plugins live in their own repositories and are federated here
as git submodules under `plugins/`.

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

This repo uses submodules. Clone with:

```bash
git clone --recurse-submodules https://github.com/patrek/patrek-ai-plugins.git
# or, after a plain clone:
git submodule update --init --recursive
```

## Adding a plugin

1. Add the plugin's repository as a submodule:

   ```bash
   git submodule add https://github.com/<owner>/<repo>.git plugins/<name>
   ```

2. Add an entry to the appropriate registry file (`.github/plugin/marketplace.json`
   for Copilot, `.claude-plugin/marketplace.json` for Claude), pointing `source`
   at `./plugins/<name>`.

3. Commit the submodule and the registry change together.

## Updating a plugin to a new release

```bash
git -C plugins/<name> fetch --tags
git -C plugins/<name> checkout <tag-or-commit>
git add plugins/<name>
```

Then bump the plugin's `version` in the registry file to match and commit.
