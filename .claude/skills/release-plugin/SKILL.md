---
name: release-plugin
description: Release a new version of a Horaizen plugin package (Claude horaizen-experiences, Claude horaizen-staff, or the ChatGPT package in openai/horaizen-experiences) from this repo. Use when the user asks to release, publish, ship, bump or tag a plugin, or after changing a package's manifest, MCP config, skills, icon or README.
argument-hint: "[plugin|chatgpt] [patch|minor|major|x.y.z] [what changed]"
---

# Release a Horaizen plugin

This repo (`horaizen-eu/horaizen-experiences-plugins`) is the `horaizen` Claude marketplace and also holds the ChatGPT package. It versions only what ships inside a package. The MCP servers deploy from the private monorepo and hosts read their tools live, so **a server deploy never needs a plugin release**. Release only when something under a package folder changed.

| Package | Folder | Tag prefix | Server |
| --- | --- | --- | --- |
| `horaizen-experiences` (Claude) | `horaizen-experiences/` | `horaizen-experiences--v` | `https://mcp.horaizenexperiences.com/mcp` (no sign-in) |
| `horaizen-staff` (Claude) | `horaizen-staff/` | `horaizen-staff--v` | `https://staff-mcp.horaizenexperiences.com/mcp` (OAuth) |
| ChatGPT package | `openai/horaizen-experiences/` | `chatgpt-horaizen-experiences--v` | `https://mcp.horaizenexperiences.com/mcp` (no sign-in) |

`<name>` below is the tag prefix without `--v`. For the ChatGPT package, its version is `version` in `openai/horaizen-experiences/plugin.json` (no `.claude-plugin/` folder), and the differences are called out in each step.

Triggering this skill is the go-ahead to prepare, validate and open the release PR. Merging is the one step that always needs the user's explicit confirmation in chat (step 6); after they confirm, tag and publish without asking again. Otherwise stop and ask only where a step below says so.

## 1. Preflight

```bash
git switch main && git pull --ff-only
git status --short          # must be empty; otherwise stop and ask what to do with the changes
gh auth status
```

## 2. Pick the plugin and find what changed

Use the plugin from the arguments. If none was given, release every plugin whose folder changed since its last tag; if none changed, say so and stop.

```bash
last=$(git tag --list '<name>--v*' --sort=-v:refname | head -1)
git log --oneline "$last"..HEAD -- <folder>/
git diff "$last"..HEAD -- <folder>/
```

If this is a release of uncommitted work the user just asked for, make that change first (on the release branch in step 4), then continue.

## 3. Choose the version

Use the version or bump type from the arguments when given. Otherwise apply semver to what the diff shows:

- **major**: server URL or auth type changes, a skill is removed or renamed, or the skill now depends on tools that older servers lack. **Ask the user to confirm before releasing a major.**
- **minor**: a new skill, new guidance that changes behavior, new listing features.
- **patch**: copy, README, keywords, icon, homepage/privacy/support URLs, typo fixes.

Current version is `version` in `<folder>/.claude-plugin/plugin.json`.

## 4. Prepare the release branch

```bash
git switch -c release/<name>-v<version>
```

- Set `version` in `<folder>/.claude-plugin/plugin.json`.
- Add a section at the **top** of `CHANGELOG.md` (below the `# Changelog` heading):

  ```markdown
  ## <name> <version> (YYYY-MM-DD)

  - <one line per user-visible change, in plain words>
  ```

## 5. Validate locally

Both must pass. Treat any error as a blocker: fix it, or stop and report it.

```bash
claude plugin validate --strict .
claude plugin validate --profile directory --strict <folder>
```

For `horaizen-experiences`, also run a live text smoke test against production (read-only):

```bash
claude -p "Using only the horaizen-experiences tools, list the operators and search the first one's experiences (limit 2, locale en). Report the tool names called and the experience names." \
  --plugin-dir <folder> \
  --allowedTools "mcp__plugin_horaizen-experiences_horaizen-experiences__*" --max-turns 6
```

It must call `list_operators` and `search_experiences` and return real names. `horaizen-staff` needs an interactive OAuth sign-in, so skip its smoke test and say so in the report.

**ChatGPT package instead:** `claude plugin validate` does not apply. Check it with:

```bash
cd openai/horaizen-experiences
python3 -c "import json; [json.load(open(f)) for f in ('plugin.json', 'mcp.json')]"
python3 -m zipfile -c "$OLDPWD/horaizen-experiences-chatgpt-<version>.zip" plugin.json mcp.json skills assets
python3 -m zipfile -t "$OLDPWD/horaizen-experiences-chatgpt-<version>.zip" && cd "$OLDPWD"
```

Confirm `plugin.json` still has five positive and three negative review test cases, every icon path exists, and the skill has `name` and `description` frontmatter. Do not commit the ZIP; it is attached to the GitHub release in step 7.

## 6. PR, CI, merge

```bash
git add -A && git commit -m "release(<name>): v<version>"
git push -u origin release/<name>-v<version>
gh pr create --base main --title "release(<name>): v<version>" --body "<changelog section>"
gh pr checks --watch
```

If `validate` fails, read the log (`gh run view --log-failed`), fix on the branch and push again. Do not merge a red PR.

When it passes, show the user the PR link, versions and changelog lines and ask them to confirm the merge. On their yes, run `gh pr merge --squash --delete-branch`, or let them merge it in GitHub and continue once `gh pr view --json state` reports `MERGED`.

## 7. Tag and publish

```bash
git switch main && git pull --ff-only
claude plugin tag <folder>      # checks plugin.json against marketplace.json, creates <name>--v<version>
git push origin refs/tags/<name>--v<version>
gh release create <name>--v<version> --title "<name> v<version>" --notes "<changelog section body>"
```

**ChatGPT package instead:** `claude plugin tag` only knows Claude plugins, so tag by hand and attach the ZIP built in step 5 (rebuild it from `main` if the branch changed):

```bash
git tag chatgpt-horaizen-experiences--v<version>
git push origin refs/tags/chatgpt-horaizen-experiences--v<version>
gh release create chatgpt-horaizen-experiences--v<version> horaizen-experiences-chatgpt-<version>.zip \
  --title "ChatGPT horaizen-experiences v<version>" --notes "<changelog section body>"
rm horaizen-experiences-chatgpt-<version>.zip
```

Uploading the ZIP to OpenAI, submitting for review and publishing are separate manual steps for the authorized developer. Tell the user the ZIP is ready; never claim it was submitted.

## 8. Verify the published install

Use a throwaway config so the user's own Claude setup is untouched:

```bash
export CLAUDE_CONFIG_DIR="$(mktemp -d)"
claude plugin marketplace add horaizen-eu/horaizen-experiences-plugins
claude plugin install <name>@horaizen
claude plugin list          # must show the new version, enabled
rm -rf "$CLAUDE_CONFIG_DIR"; unset CLAUDE_CONFIG_DIR
```

Skip this step for the ChatGPT package; instead confirm the release page lists the ZIP (`gh release view <tag>`).

## 9. Report

Tell the user: plugin and version, the changelog lines, PR and release links, validation and smoke-test results, and anything skipped. Existing users get the new version with `/plugin marketplace update horaizen` (or automatically if they enabled auto-update).

## Rules

- Never put secrets, tokens, private hostnames or customer data in this public repo.
- Never move or delete an existing tag; ship a new patch instead.
- Every package gets its own version, changelog section, tag and GitHub release. When one change touches several packages (a shared URL, a rename), release them together in one PR, then tag each.
- Keep the Claude and ChatGPT `browse-experiences` skills consistent in substance; only host wording differs.
- Keep the marketplace entry in `.claude-plugin/marketplace.json` in sync if a plugin's description or folder changes.
