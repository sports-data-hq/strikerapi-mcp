# StrikerAPI: soccer skills

Soccer analysis skills on StrikerAPI: game previews, player and team research, and the community's standard metrics, with every number pulled live from the StrikerAPI MCP server.

## Skills

- `soccer-fpl-captain`: Use when someone plays Fantasy Premier League and picks a captain.
- `soccer-fpl-transfers`: Use when someone plays Fantasy Premier League and is planning transfers.
- `soccer-match-preview`: Use when the user wants a preview of one soccer (football) match in any covered league.
- `soccer-player-props-research`: Use when the user asks about soccer player prop prices as information.
- `soccer-season-simulation`: Use when the user wants odds for how a soccer league season finishes, in fan or analyst wording.
- `soccer-set-pieces`: Use when the user wants set-piece numbers in soccer.
- `soccer-team-style-profile`: Use when the user asks how a soccer club plays, in fan or analyst wording.
- `soccer-xg-breakdown`: Use when the user asks whether a soccer result, club or player matches the chances created, in fan or analyst wording.

## Data it sends

The skills call the StrikerAPI MCP server at `mcp.strikerapi.com`. Each call sends the tool name and its inputs (teams, players, dates, seasons) with your StrikerAPI API key, and returns data. Nothing from your files or conversation is sent beyond what a tool call needs. Calls use credits on your StrikerAPI account; a free key at https://strikerapi.com/signup includes monthly credits.

Privacy policy: https://strikerapi.com/privacy

## Install

```
/plugin marketplace add sports-data-hq/sportsdatahq-skills
/plugin install soccer-data@sportsdatahq
```

More: https://strikerapi.com/skills. Licence: MIT (see LICENSE).
