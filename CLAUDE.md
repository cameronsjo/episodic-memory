# Episodic Memory - Fork Maintenance

## Fork Relationship

- **Origin (push)**: `cameronsjo/episodic-memory`
- **Upstream (pull)**: `obra/episodic-memory`

## Syncing Upstream

```bash
git fetch upstream
git merge upstream/main
```

This repo has active upstream development. Be careful merging — test after syncing.

## Where Customizations Live

- `README.md` — re-owned header
- `CLAUDE.md` — this file (fork-only)
- Any MCP server or indexing modifications

## What Not to Touch When Merging

- `src/` core indexing/embedding logic — accept upstream unless you've intentionally diverged
- `.claude-plugin/plugin.json` version bumps — accept upstream
- `package.json` dependency updates — accept upstream, then `npm install`

## Local Development

```bash
npm install
npm test
npm run build
```
