# Horaizen Experiences plugins for Claude

Claude plugins for [Horaizen Experiences](https://horaizenexperiences.com), the booking platform for tour and rental operators.

| Plugin | For | Server | Sign-in |
| --- | --- | --- | --- |
| [`horaizen-experiences`](horaizen-experiences) | Customers browsing experiences | `https://mcp.horaizenexperiences.com/mcp` | None, read-only |
| [`horaizen-staff`](horaizen-staff) | Operator staff | `https://staff-mcp.horaizenexperiences.com/mcp` | Horaizen account, OAuth with per-organization consent |

## Install

In Claude Code:

```
/plugin marketplace add horaizen-eu/claude-plugins
/plugin install horaizen-experiences@horaizen
/plugin install horaizen-staff@horaizen
```

In Claude on the web or desktop, add the server URL above as a custom connector.

## How releases work

The MCP servers are developed and deployed from Horaizen's private repository. Claude reads their tool lists live, so server deploys reach every user without a plugin release.

This repository versions only what ships in a plugin: the manifest, `.mcp.json`, skills, icon and README. To release a plugin:

1. Bump `version` in its `.claude-plugin/plugin.json` and add an entry to `CHANGELOG.md`.
2. Merge after the `validate` workflow passes.
3. Tag it with `claude plugin tag <plugin-folder>` (creates `<name>--v<version>`) and push the tag.

## Support

https://horaizenexperiences.com/en/contact
