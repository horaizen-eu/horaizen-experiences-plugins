# Horaizen Experiences

Browse experiences from participating Horaizen operators, see public catalog photos and details, and check advisory availability for a day and party size. Bookable departures link to the operator's booking website, where booking and payment happen.

## Install

Claude Code:

```
/plugin install horaizen-experiences --marketplace horaizen-eu/claude-plugins
```

Claude on the web or desktop: add `https://mcp.horaizenexperiences.com/mcp` as a custom connector to use the interactive picker.

## What it includes

- **MCP server** `horaizen-experiences` (`https://mcp.horaizenexperiences.com/mcp`, Streamable HTTP, no sign-in). Five read-only tools: `list_operators`, `get_operator`, `search_experiences`, `get_experience`, `get_availability`.
- **Interactive picker** (MCP Apps, `ui://horaizen/experience-picker-v2.html`) for hosts that render MCP Apps, such as Claude on the web and desktop. Text-only hosts receive the same results as JSON.
- **Skill** `browse-experiences` with guidance for safe, accurate discovery.

## What it does not do

No bookings, holds, payments, refunds, passenger details or staff data. Availability is a snapshot and is confirmed on the booking website.

## Example prompts

- "Which experience operators can I browse?"
- "Help me find an experience for tomorrow for two people."
- "Quero ver os detalhes e fotografias de uma experiência."

## Support

https://horaizenexperiences.com/en/contact
