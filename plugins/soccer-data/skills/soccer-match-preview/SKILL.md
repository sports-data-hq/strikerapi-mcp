---
name: soccer-match-preview
description: "Use when the user wants a preview of one soccer (football) match in any covered league: 'preview Arsenal vs Chelsea', 'break down the Clasico', 'what should I know about the Inter match', 'MLS preview LAFC vs Galaxy', 'Champions League preview'. Produces a one-page report: table position and form, our club Elo, xG for and against, lineups when out, the three-way line and what it implies. Not for one match's shot-by-shot xG story (see soccer-xg-breakdown) or modeling (see model-building)."
metadata:
  version: 2.2.0
  author: SportsDataHQ
---

# Soccer match preview

A one-page preview of one match from live StrikerAPI calls. The default league is the Premier League; pass `league` for others (mls, laliga, seriea, bundesliga, ligue1, ucl, championship, nwsl, ligamx, uel, uecl, eredivisie, primeira, scotland, brazil, argentina).

## Before you start
- For anything across games, seasons or players, apply the sports-data-preflight checks first (ids from search, the season convention, one league, enough data).
- Tools come from the StrikerAPI MCP server (`https://mcp.strikerapi.com/mcp`). Call them; never recall a number.
- `season` is the year the season starts (2025 = 2025-26); MLS, NWSL, Liga MX, Brazil and Argentina use calendar years.
- Clubs accept abbreviations or names (`list_teams`, 1 cr).

## Default path (about 22 credits)
1. **Find the match.** `get_schedule(team)` (2 cr): fixture id, kickoff, the median three-way line and its implied probabilities.
2. **Table and form.** `get_standings(season)` (2 cr): position, points, goal difference, home/away split, form.
3. **Strength.** `get_ratings()` (2 cr): our club Elo. A 100-point gap is about a 64% expected score for the stronger side at a neutral venue.
4. **Underlying numbers.** `get_team_stats(season, team)` (5 cr) for both: goals and our xG for and against per match, shots, possession.
5. **History (optional).** `get_head_to_head(team, opponent)` (2 cr): weak evidence, say so.
6. **Who is out.** `get_availability(team, absent_only: true)` (5 cr) for both clubs: players missing from their club's last two or more matchday squads (`absent`) or the latest one, with last appearance. It can't say injury from suspension or selection; say so.
7. **Lineups (when out, about an hour before kickoff).** `get_lineups(game_id)` (5 cr): formations and starting XIs.
8. **What the market implies (optional).** `get_game_probabilities(game_id)` (5 cr): no-margin home/draw/away, totals and handicaps across books.

## Output template
```
<HOME> (<pos>, <pts>) vs <AWAY> (<pos>, <pts>) | <competition> | <date, kickoff>
Line: home <x> / draw <y> / away <z> | Market (no-margin): <h%> / <d%> / <a%>
Elo: <home> vs <away> (gap <n>)
Form: last 5 for both (W/D/L, GF-GA)
xG per match (season): for / against for both, vs actual goals (over/under-performance)
Out: <absent players per club from get_availability, with last appearance> | Lineups: <formations> or "not announced yet"
What to watch: 2-4 bullets tied to the numbers
Data: get_schedule, get_standings, get_ratings, get_team_stats | <league>, season <yyyy>
```

## When there's no game

If the game the user names isn't scheduled (off-season, not booked, already played), don't stop to ask. Do the most useful real version and say which one in the first line:
1. The next scheduled meeting of those two sides, if `get_schedule` (filtered by team) has one.
2. Otherwise their most recent meeting and where each side finished last season, from the last completed season's data.
3. For a matchup that can't happen soon, the ratings matchup labelled as hypothetical.
Answer first; offer the alternative in one closing line, never as a question before answering.

## Rules
- Our xG is a location model (distance, angle, header or not); it does not see defenders. Say "our xG" and keep it.
- Show matches played behind per-match rates; under 8 matches is noisy.
- Three outcomes: never talk about a two-way "win probability" without the draw.
- Odds are information about the market: no side, stake or bet.
- Complete before writing: every step in the default path is called for both sides before the preview is written. If a call fails or returns nothing, say which section is missing rather than writing around it.
- Sequences (last 5, recent form, a run of results) are quoted in the order the tool returns them, with dates; never retell them from memory. Lists in the answer must match the tool rows.
- Out of credits: if a call is refused for credits, pass on the API's message as it comes (it says when free credits refill and how to add more), and stop; don't retry or switch tools to get around it.