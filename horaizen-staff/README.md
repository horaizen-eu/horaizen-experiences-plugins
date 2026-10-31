# Horaizen Staff

Connects Claude to your Horaizen Experiences organization through the staff MCP server (`https://staff-mcp.horaizenexperiences.com/mcp`).

## Sign-in

The first tool use opens Horaizen sign-in. Claude registers itself automatically, then you choose one organization and the exact permissions to allow. Every request rechecks your current membership and permissions. Revoke access at any time from the connections page in the dashboard.

## Install

Claude Code:

```
/plugin install horaizen-staff --marketplace horaizen-eu/claude-plugins
```

Claude on the web or desktop: add `https://staff-mcp.horaizenexperiences.com/mcp` as a custom connector.

## Safety

Read tools, draft-only replies and real writes are labelled separately. Booking writes can create customers and send emails; experience writes change the catalog. Claude asks before running write tools.

## Support

https://horaizenexperiences.com/en/contact
