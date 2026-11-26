---
name: soccer-set-pieces
description: "Use when the user wants set-piece numbers in soccer: 'how many goals do Arsenal score from corners', 'who takes the set pieces', 'are they good at defending corners', 'penalty record this season', 'free kick goals', 'set piece dependence'. Pulls set-piece events from match event data, counts them and what came from them, and says when the events are not held for a league or season. Not for a match preview (see soccer-match-preview), shot quality by location (see soccer-xg-breakdown) or playing style (see soccer-team-style-profile)."
metadata:
  version: 2.0.0
  author: SportsDataHQ
---

# Soccer set pieces

Set pieces are a share of goals that general shot data blurs. This skill pulls the set-piece events StrikerAPI holds for matches, counts them, and reports only what the rows show.

## Before you start
- For anything across games, seasons or players, apply the sports-data-preflight checks first (ids from search, the season convention, one league, enough data).
- Tools come from the StrikerAPI MCP server (`https://mcp.strikerapi.com/mcp`). Call them; never fill in a number from memory.
- Seasons are named by the year they start (2025 = 2025-26); MLS, NWSL, Liga MX, Brazil and Argentina are calendar years. Pass `league` (soccer is the Premier League). Club ids from `list_teams` (1 cr), players from `search_players` (1 cr), matches from `list_games` (2 cr).
- `get_events(game_id, kind: "set_pieces")` (10 cr) is the only set-piece source. Check first: pull one match and confirm set-piece rows come back. Older matches carry goals, cards and substitutions only, and some leagues hold no shot detail, so an empty result means not held, not zero.
- Read the field names returned (type of set piece, team, taker, outcome) before using them. If a taker or outcome field is absent, say so and do not infer it. Shots carry location, body part and xG on `get_events` (kind shots); whether a shot came from a set piece is only known if a row says so.
- Set-piece data is per match, so a season needs many calls. Cap the scope (one team's last 5 to 10 matches) and say how many matches were read.

## Default path (about 50 to 120 credits for a team over several matches)
1. **Resolve scope.** `list_teams(league)` (1 cr), then `list_games(team, season)` (2 cr) for match ids and dates.
2. **Check the tags.** `get_events(game_id, kind: "set_pieces")` (10 cr) for one match. If no rows or only goals, report "set-piece events not held for this league or season" and stop.
3. **Pull the sample.** The same call for each match in scope (10 cr each). Count set pieces for and against by type, and the goals, shots and shots on target that the rows link to them.
4. **Takers.** Group by taker from the rows, if a taker field is returned.
5. **Context (optional).** `get_team_stats(team, season)` (5 cr) for corners, goals and xG per match, for and against, to put set-piece counts beside the team's overall volume.

## Reading it
Set-piece goal counts are small: a team with 6 set-piece goals in 10 matches says little about next month. Show matches read and events counted, call fewer than 30 set pieces of one type descriptive only, and never call a team good or bad at set pieces from goals alone. Compare for and against separately. xG on a set-piece shot comes from our location model, which does not see defenders.

## Output template
```
<Team> | <league, season> | matches read <n> of <m> | set-piece events <n>
For: corners <a>, free kicks <b>, penalties <c> | goals from them <g>, shots <s>, shots on target <t>
Against: corners <a>, free kicks <b>, penalties <c> | goals conceded from them <g>
Takers (if returned): <name: count>
Share of all goals: <g from set pieces> of <all goals> (all goals from get_team_stats)
Read: 2-3 bullets with counts, thin samples flagged
Data: list_teams, list_games, get_events, get_team_stats | <league>, season <yyyy>
```

## Rules
- Never claim a set-piece pattern the rows do not show; if the type or taker field is missing, say so.
- State matches read of matches played; a partial sample is not a season figure.
- Do not discuss delivery type, routines or marking: they are not in the data.
- Every number names its tool (the Data line, plus inline where it could be doubted) and every rate shows its sample size.
- If a tool returns too little, say "not enough data" and stop; do not estimate or borrow another sample.
- Odds and prices are information about the market only: no picks, leans, stakes, payouts or sportsbook links.
- Sequences (last 5, recent form, a run of results) are quoted in the order the tool returns them, with dates; never retell them from memory. Lists in the answer must match the tool rows.
- Out of credits: if a call is refused for credits, pass on the API's message as it comes (it says when free credits refill and how to add more), and stop; don't retry or switch tools to get around it.

## Example prompts
- "How many goals did Arsenal score from corners?"
- "Who takes Liverpool's set pieces?"
- "Set-piece goals conceded by Brentford"
