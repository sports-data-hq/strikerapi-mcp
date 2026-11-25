---
name: soccer-team-style-profile
description: "Use when the user wants to know how a soccer club plays: 'what is Brighton's style', 'how does Arsenal build up', 'do they press high', 'who counter-attacks', 'compare Liverpool and City styles', 'does this team take shots from distance', 'is it a possession team'. Profiles possession, shot volume and quality, set pieces, discipline and where shots come from, for and against, relative to the league, with the formulas stated. Not for one match's xG story (see soccer-xg-breakdown), match previews (see soccer-match-preview) or finishing positions (see soccer-season-simulation)."
metadata:
  version: 2.0.0
  author: SportsDataHQ
---

# Soccer team style profile

Style shows up in a handful of per-match averages: how much of the ball a club has, how many shots it creates and allows, how good they are, and how it gets them. This skill reports those against the league, with every derived number's formula stated.

## Before you start
- For anything across games, seasons or players, apply the sports-data-preflight checks first (ids from search, the season convention, one league, enough data).
- Tools come from the StrikerAPI MCP server (`https://mcp.strikerapi.com/mcp`). Call them; never fill in a number from memory.
- `season` is named by the year it starts (2025 = 2025-26); MLS, NWSL, Liga MX, Brazil and Argentina use calendar years. Pass `league` (soccer is the Premier League; others by key). Clubs accept names or abbreviations (`list_teams`, 1 cr).
- `get_team_stats` returns per-match averages for and against: goals, our xG, shots, shots on target, possession, pass accuracy, corners, fouls, cards, tackles and clean sheets. Possession is missing for some leagues and seasons (for example the Eredivisie before 2020, the Scottish Premiership 2015 to 2023, the NWSL before 2025); if it is absent, say so and leave possession out.
- Pressing intensity, passes per defensive action and build-up patterns are not returned. Do not describe a club as a high-pressing side from fouls or tackles alone; say what the numbers show and no more.
- Our xG is a location model (distance and angle); it does not see defenders or the pass.

## Default path (about 12 to 22 credits)
1. **League baseline.** `get_team_stats(league, season)` (5 cr) without `team`: every club's averages, so each club can be placed against the league mean and rank. If the tool returns only one club per call, say the league baseline is limited to the clubs you pulled.
2. **The club.** `get_team_stats(team, league, season)` (5 cr) if the baseline did not cover it.
3. **Shape (optional).** `get_lineups(game_id)` (5 cr) for the usual formation and starting XI in recent matches.
4. **Where shots come from (optional).** `get_shot_map(team, season)` (10 cr), and with `against: true` for shots conceded: zones and xG.
5. **Table position (optional).** `get_standings(season, league)` (2 cr) for context.

## Derived measures (formulas)
- Shot quality = xG per shot = xG for / shots for (and the same against).
- Shot share = shots for / (shots for + shots against).
- xG difference per match = xG for - xG against.
- Finishing gap = goals for - xG for, per match.
- Set-piece reliance is not in the data unless the tool returns it; do not compute it from corners alone.
Show each next to the league mean and the club's rank, and say how many matches the averages cover.

## Output template
```
<Club> | <league, season> | matches <n>
Possession <%> (rank <r>) or "not returned for this league and season"
Shots for / against per match: <a> / <b> | shot share <%> | on target <%>
Shot quality: xG per shot <v> for, <v> against (formula: xG / shots) | league mean <v>
Territory and chances: xG for <v>, xG against <v>, difference <v>
Discipline: fouls <v>, cards <v>, tackles <v>
Style read: 2-3 bullets stating only what these numbers show
Data: get_team_stats[, get_lineups, get_shot_map] | <league>, season <yyyy>
```

## Rules
- State the formula for every derived number once.
- Per-match averages hide opponent strength and game state; say so.
- Do not use style labels ("counter-attacking", "high press") the data does not support.
- Every number names its tool (the Data line, plus inline where it could be doubted) and every rate shows its sample size.
- If a tool returns too little, say "not enough data" and stop; do not estimate or borrow another sample.
- Odds and prices are information about the market only: no picks, leans, stakes, payouts or sportsbook links.

## Example prompts
- "What is Brighton's style of play?"
- "Compare Liverpool and Manchester City's styles"
- "Does Arsenal create good or lots of chances?"
