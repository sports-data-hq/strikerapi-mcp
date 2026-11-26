---
name: soccer-fpl-transfers
description: "Use when someone plays Fantasy Premier League and is planning transfers: 'who do I bring in', 'should I take a hit', 'sell him or hold', 'best forwards for the next five gameweeks', 'fixture run for Arsenal', 'who is nailed on for minutes', 'wildcard draft', 'which defense has the best fixtures', 'free transfer or save it', 'replace my injured midfielder'. Rates the user's squad and candidates by expected points over the next gameweeks from minutes, our xG, fixture strength from team goals for and against, and anytime-scorer prices. Also for bare follow-ups with no names: load this skill first, then ask for what is missing, rather than answering from memory. Not for the captain pick (see soccer-fpl-captain), a match preview (see soccer-match-preview), prop research or other fantasy games. Prices and ownership must be pasted."
metadata:
  version: 1.0.0
  author: SportsDataHQ
---

# FPL transfer planner

An FPL transfer is worth making when the points gained over the next few gameweeks beat the cost, which is the 4-point hit if you spend more than your free transfers. The signals the data holds are minutes, shot-based scoring rates, fixtures and the market's goal expectations. Prices, ownership, injury news and gameweek dates are not in the data, so the user supplies them.

## Before you start
- For anything across games, seasons or players, apply the sports-data-preflight checks first (ids from search, the season convention, one league, enough data).
- Tools come from the StrikerAPI MCP server. `season` is the year the season starts (2025 = 2025-26). The default league is the Premier League; pass `league` for others. Player ids come from `search_players(name)` (1 cr).
- Ask the user to paste: their 15 players (positions, teams, selling prices if known), money in the bank, free transfers, and the prices of any targets. Prices, ownership and chip status are not in the data; never state a price from memory.
- Availability: `get_availability(team)` (5 cr a club, 10 the whole league; `absent_only: true` for just the missing) for the clubs involved; never bring in a player marked `absent`, and treat `missed_last_squad` as a minutes risk. There is no injury report, so news not yet visible in the squads comes from the user; say a player's minutes risk is unconfirmed unless the log shows him playing.
- Gameweek deadlines and double or blank gameweeks are mapped from fixture dates; ask the user for the deadline date if it matters.

## Default path (about 40 to 100 credits)
1. **Fixtures.** `get_schedule(days: 30)` (2 cr): the next fixtures per club with dates; count games per club per gameweek window.
2. **Team strength.** `get_team_stats(season)` (5 cr, once for the league): goals and xG for and against per match for every club.
3. **Squad and candidates.** `search_players(name)` (1 cr each), then `get_player(player_id)` (5 cr) for per-90 rates and minutes; `get_player_games(player_id, limit: 5)` (5 cr) for the last 5 matches and minutes. Find candidates with `get_leaders(stat: xg, per90: true, min_games: 5, limit: 30)` (5 cr) and `get_leaders(stat: goal_contributions, per90: true)` (5 cr).
4. **Market view (top candidates only).** `get_player_props(game_id, market: player_goal_scorer_anytime)` (10 cr per match; `estimate_only: true` first) for the next fixture.

## Projection method
Expected FPL points per fixture for a player (default scoring: 2 for 60+ minutes, goals 6 for goalkeepers and defenders, 5 for midfielders, 4 for forwards, assists 3, clean sheet 4 for goalkeepers and defenders and 1 for midfielders; confirm the user's league rules):
- P60 = share of his last 5 league matches in which he played 60 or more minutes (from `get_player_games`, n shown); minutes points = 2 x P60.
- Expected goals E[g] = 90-minute goal rate x P60 x the fixture factor. Goal rate = our xG per 90 over this season (from `get_player`, minutes shown). Fixture factor = the opponent's expected goals conceded in this fixture divided by the league average (from `get_team_stats`: average of the opponent's goals conceded and our club's goals scored per match, no home or away split, say so). Where an anytime-scorer price exists (`get_player_props`, top five leagues, MLS and the Champions League), also show -ln(1 - p) as the market's expected goals; it uses the price with margin, so it runs slightly high.
- Expected assists E[a] = assists per 90 this season x P60 x the same fixture factor (noisy: show the minutes behind it).
- Clean sheet probability = exp(-L), where L = the opponent's expected goals in this fixture (same blend); points = P60 x P(clean sheet) x the position's clean-sheet value.
- xP = 2 x P60 + goal points x E[g] + 3 x E[a] + clean sheet points. Bonus points, cards, saves and defensive-contribution points are not modelled and the number is a floor-style projection, not a forecast.
Transfer logic:
- Horizon = the next 4 gameweeks (3 to 6 if the user asks); xP is the sum of per-fixture xP over the fixtures in the window, so double gameweeks count twice and blanks count zero.
- Transfer gain = candidate's horizon xP - the player sold's horizon xP, minus 4 for each transfer beyond the user's free transfers. Recommend a transfer only when the gain is positive and the player in has at least 4 of 5 recent matches with 60 minutes or more; say a gain under about 1 point over the horizon is within noise.
- Budget: the sale and purchase must fit the user's bank with their pasted prices; if prices are missing, rank by xP and say affordability is unchecked.
- Save-or-use: if the best transfer gain over the horizon is under the hit cost or the user has two free transfers banked, say holding is reasonable.
- Never suggest a chip date; chip plans depend on the user's remaining chips and the deadline dates they give.

## Output template
```
Next <n> gameweeks, window <date a> to <date b> (<season>)
Squad xP (sorted): <player (team): xP, P60 (n), fixtures <opp list>, flag>
Candidates: <player: xP, P60 (n), E[g], E[a], price (yours), fixtures>
Best moves: <out> -> <in>: gain <x> over <n> fixtures, hit <0/4>, net <y>
Fixture run: <team: games, avg opp goals conceded>
Availability: <status from get_availability> | Unchecked: <prices, ownership, news not yet in squads>
Data: get_schedule, get_team_stats, get_player, get_player_games, get_leaders, get_player_props | season <yyyy>
```

## When there is no game or no line
If the club has no fixtures in the window (blank), xP is zero and the player should be flagged. Before about 5 matches into the season, rates are thin: mark them 'not enough data' and lean on last season's minutes. In the international break or offseason, say there are no fixtures and plan from the next window. If the user's league is not the Premier League, pass its league and say prices still come from them.

## Rules
- Fantasy rankings and advice only: no betting language. A prop line is used here only as the market's median projection of a player's stat.
- The user's scoring and roster settings come from the user; if unstated, say which default you used and show the formula so they can swap it.

- Every number names its tool (the Data line, plus inline where it could be doubted) and every rate shows its sample size and season convention.
- Do not add names, roles, injuries or storylines from memory; if a tool did not return it, leave it out.
- If a tool returns too little, say "not enough data" and stop; do not estimate or borrow another sample.
- Sequences (last 5, recent form, a run of results) are quoted in the order the tool returns them, with dates; never retell them from memory. Lists in the answer must match the tool rows.
- Out of credits: if a call is refused for credits, pass on the API's message as it comes (it says when free credits refill and how to add more), and stop; don't retry or switch tools to get around it.
