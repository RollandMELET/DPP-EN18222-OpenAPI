# AGENTS.md

Rules for any AI agent (Claude Code, Codex or other) working on this repository.

## Pull requests: the maintainer merges, agents never do

This repository is public and must stay public. Rolland Mélet is the only
person who approves and merges pull requests. Agents may act with his GitHub
token, so GitHub cannot tell them apart from him: this rule is written here
for that reason.

- An agent opens pull requests. It never merges them: `gh pr merge` is
  forbidden, including with `--admin` or `--auto`.
- No direct push to `main`. No change to branch protection, CODEOWNERS or
  repository visibility.
- The agent presents the pull request to the maintainer (URL and what it
  changes) and waits for him to merge it.

## Content

- No text of the standards is copied. Cite EN numbers, titles and method names only.
- Everything published here is in English.
- Lint before opening a pull request: `npx @redocly/cli lint openapi.yaml`.
