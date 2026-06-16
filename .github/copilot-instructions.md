# patrek-ai-plugins

A plugin marketplace meta-repository for AI coding CLIs. It contains no application
code and no plugin source of its own — it is just two registry files. Plugins live in
their own repositories and are federated here **by reference**. There is no build,
test, or lint tooling.

## Architecture: dual scoped registries

The repo maintains two separate marketplace registries so each CLI only sees plugins
it can actually run:

| Tool               | Registry file                     | Status                |
| ------------------ | --------------------------------- | --------------------- |
| GitHub Copilot CLI | `.github/plugin/marketplace.json` | lists `warp-notifications` |
| Claude Code        | `.claude-plugin/marketplace.json` | currently `plugins: []` |

These are **independent lists, not mirrors.** A plugin only belongs in a registry if
that tool can run it. `copilot-warp` is a Copilot CLI plugin (it uses Copilot's
`notification` hook), so it appears only in the Copilot registry; the Claude registry's
`plugins` array is intentionally empty until a Claude-runnable plugin is added. Do not
"sync" entries between the two files.

## Federation: `source` objects, NOT submodules

This repo deliberately does **not** use git submodules. The Copilot CLI does not resolve
submodules — a relative `source` path only works if the plugin's files physically exist
in this repo's tree, which they don't. Instead, each plugin entry's `source` is an object
pointing at the plugin's own GitHub repository, pinned to a tag:

```json
"source": {
  "source": "github",
  "repo": "patrek/copilot-warp",
  "ref": "v0.1.0"
}
```

- `repo` — `owner/repo` of the plugin's own repository.
- `ref` — a tag (or commit SHA) to pin to. **Always pin.** Omitting `ref` tracks the
  default branch, so an upstream push silently changes what users install.
- `path` — add this only if the plugin's `plugin.json` is in a subdirectory rather than
  the repo root (`copilot-warp`'s manifest is at its root, so no `path` is used).

The CLI clones the referenced repo at install time (`copilot plugin install
warp-notifications@patrek-ai-plugins`). Consumers do **not** need `--recurse-submodules`.

## Conventions

- The registry entry's `version`, the `ref` tag, and the upstream plugin's own
  `plugin.json` version must all stay in lockstep. Bump them together.
- A `ref` must already exist in the plugin's upstream repository. To release a new
  version, create and push the tag in that repo first, then update `ref` + `version` here.
- Keep `README.md` (the human-facing "Plugins" table and add/update instructions)
  consistent with registry changes.
- This repo holds no plugin source. Plugin behavior changes happen in the plugin's own
  repository; here you only update the reference.
