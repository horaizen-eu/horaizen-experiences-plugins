# Horaizen Experiences plugins

Official AI assistant plugins for [Horaizen Experiences](https://horaizenexperiences.com), the booking platform for tour and rental operators. Published by Psilodigital, Lda.

| Package | Host | For | Server | Sign-in |
| --- | --- | --- | --- | --- |
| [`horaizen-experiences`](horaizen-experiences) | Claude | Customers browsing experiences | `https://mcp.horaizenexperiences.com/mcp` | None, read-only |
| [`horaizen-staff`](horaizen-staff) | Claude | Operator staff | `https://staff-mcp.horaizenexperiences.com/mcp` | Horaizen account, OAuth |
| [`openai/horaizen-experiences`](openai/horaizen-experiences) | ChatGPT | Customers browsing experiences | `https://mcp.horaizenexperiences.com/mcp` | None, read-only |

## Install

### Claude Code

```
/plugin marketplace add horaizen-eu/horaizen-experiences-plugins
/plugin install horaizen-experiences@horaizen
/plugin install horaizen-staff@horaizen
```

Update later with `/plugin marketplace update horaizen`.

### Claude on the web or desktop

Add the server URL from the table as a custom connector. The customer server renders an interactive experience picker (MCP Apps) there.

### ChatGPT

The ChatGPT package is submitted through OpenAI's plugin submission. Each release attaches the upload ZIP to its [GitHub release](https://github.com/horaizen-eu/horaizen-experiences-plugins/releases).

## What each plugin does

**`horaizen-experiences` (customers).** Five read-only tools: `list_operators`, `get_operator`, `search_experiences`, `get_experience`, `get_availability`. Customers browse approved operators, see public photos and details, and check advisory availability for a day and party size. Bookable departures link to the operator's booking website, where booking and payment happen. It creates no bookings, holds or payments and reads no customer data.

**`horaizen-staff` (operator staff).** Bookings, availability, experiences, customer history, revenue, internal notes and draft replies for one organization. On first use Claude registers itself and opens Horaizen sign-in; staff choose one organization and the exact permissions to allow. Every request rechecks current membership and permissions. Read tools, draft-only replies and real writes are labelled separately, and Claude asks before running writes. Revoke access from the dashboard's connections page.

## Repository layout

```text
.claude-plugin/marketplace.json   # the "horaizen" Claude marketplace
horaizen-experiences/             # Claude plugin: manifest, .mcp.json, skill, icon
horaizen-staff/                   # Claude plugin: manifest, .mcp.json, icon
openai/horaizen-experiences/      # ChatGPT package: plugin.json, mcp.json, skill, icon
.claude/skills/release-plugin/    # release procedure, run as /release-plugin
.github/workflows/validate.yml    # validation on every PR and push
```

## Releases

The MCP servers are developed and deployed from Horaizen's private repository. Hosts read their tool lists live, so a server deploy reaches every user without a plugin release. This repository versions only what ships inside a package: manifests, MCP config, skills, icons and READMEs.

Each package is versioned and tagged on its own:

| Package | Tag |
| --- | --- |
| `horaizen-experiences` | `horaizen-experiences--v<version>` |
| `horaizen-staff` | `horaizen-staff--v<version>` |
| ChatGPT package | `chatgpt-horaizen-experiences--v<version>` |

To release, open Claude Code in this repository and run:

```
/release-plugin <package> [patch|minor|major|x.y.z]
```

The skill bumps the version, updates `CHANGELOG.md`, validates, opens and merges the PR once CI passes, tags, publishes the GitHub release (with the ZIP for ChatGPT) and checks the published install. See [`.claude/skills/release-plugin/SKILL.md`](.claude/skills/release-plugin/SKILL.md) for the full procedure. Submitting the ChatGPT ZIP to OpenAI stays a manual step.

## Privacy, support and license

- Privacy policy: https://www.horaizenexperiences.com/en/privacy-policy
- Support: https://horaizenexperiences.com/en/contact
- License: [MIT](LICENSE). The Horaizen name and logo are not licensed for reuse.
