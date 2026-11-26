---
name: soccer-fpl-captain
description: "Use when someone plays Fantasy Premier League and picks a captain: 'who do I captain', 'Haaland or Salah armband', 'vice captain', 'captain differential', 'double gameweek captain', 'safe captain pick', 'is it worth captaining him away', 'Triple Captain target', 'rank my top three for the armband'. Ranks the user's shortlist by expected points doubled, from minutes security, our xG rate, the opponent, and anytime-scorer, shots and assist prices where posted. Also for bare follow-ups with no names: load this skill first, then ask for what is missing, rather than answering from memory. Not for transfers (see soccer-fpl-transfers), match previews (see soccer-match-preview) or prop research (see soccer-player-props-research). Ownership must be pasted to call a differential."
metadata:
  version: 1.0.0
  author: SportsDataHQ
---

# FPL captain and armband ranking

A captain doubles one player's points, so the choice rewards minutes security plus goal threat in the fixture. The market's price for an anytime goal is the cleanest single read of that threat, and our shot-based rates and the opponent's record fill in when no price exists. Ownership is not in the data, so differential talk needs the user's pasted figure.

## Before you start
- For anything across games, seasons or players, apply the sports-data-preflight checks first (ids from search, the season convention, one league, enough data).
- Tools come from the StrikerAPI MCP server. `season` is the year the season starts (2025 = 2025-26). The default league is the Premier League; pass `league` for others. Player ids come from `search_players(name)` (1 cr).
- Ask for the shortlist (or the whole squad). Ownership percentages are not in the data; without them, give the ranking only and say the differential call needs ownership.
- Availability: `get_availability(team)` (5 cr a club, 10 the whole league; `absent_only: true` for just the missing) for each shortlisted player's club. `absent` means missing from the club's last two or more matchday squads (injured, suspended, ill or dropped; the tool can't say which), `missed_last_squad` means the latest one. Drop an absent player from the ranking and say why; flag a missed-last-squad player as a minutes risk. There is no injury report, so still ask the user for news the squads can't show yet.
- Anytime-scorer, shots, shots on target and assist prices exist for the Premier League, La Liga, Serie A, Bundesliga, Ligue 1, MLS and the Champions League; check with `estimate_only` before spending credits.

## Default path (about 30 to 70 credits for a shortlist of 3 to 5)
1. **Fixtures.** `get_schedule(days: 7)` (2 cr): each shortlist player's fixtures this gameweek (two means a double), with the median three-way line and goal total.
2. **Recent minutes.** `search_players(name)` (1 cr), `get_player_games(player_id, limit: 5)` (5 cr): minutes, goals, assists, xG, shots for the last 5 matches.
3. **Rates.** `get_player(player_id)` (5 cr): per-90 xG and assists this season, minutes behind them.
4. **Opponent.** `get_team_stats(season)` (5 cr, once): goals and xG against per match for the opponents.
5. **Market view.** `get_player_props(game_id, estimate_only: true)` (free), then `get_player_props(game_id, market: player_goal_scorer_anytime)` (10 cr per match) and, if wanted, `get_prop_probabilities(game_id, player_id)` (10 cr) for shots and assists; the price has the margin in, so say expected goals run slightly high.

## Projection method
Expected FPL points per fixture for a player (default scoring: 2 for 60+ minutes, goals 6 for goalkeepers and defenders, 5 for midfielders, 4 for forwards, assists 3, clean sheet 4 for goalkeepers and defenders and 1 for midfielders; confirm the user's league rules):
- P60 = share of his last 5 league matches in which he played 60 or more minutes (from `get_player_games`, n shown); minutes points = 2 x P60.
- Expected goals E[g] = 90-minute goal rate x P60 x the fixture factor. Goal rate = our xG per 90 over this season (from `get_player`, minutes shown). Fixture factor = the opponent's expected goals conceded in this fixture divided by the league average (from `get_team_stats`: average of the opponent's goals conceded and our club's goals scored per match, no home or away split, say so). Where an anytime-scorer price exists (`get_player_props`, top five leagues, MLS and the Champions League), also show -ln(1 - p) as the market's expected goals; it uses the price with margin, so it runs slightly high.
- Expected assists E[a] = assists per 90 this season x P60 x the same fixture factor (noisy: show the minutes behind it).
- Clean sheet probability = exp(-L), where L = the opponent's expected goals in this fixture (same blend); points = P60 x P(clean sheet) x the position's clean-sheet value.
- xP = 2 x P60 + goal points x E[g] + 3 x E[a] + clean sheet points. Bonus points, cards, saves and defensive-contribution points are not modelled and the number is a floor-style projection, not a forecast.
Captain points = 2 x xP for the fixture (the armband doubles every item), summed over both fixtures in a double gameweek.
- Rank the shortlist by captain xP; show P60 with n, E[g] and the market's expected goals side by side. When the two goal estimates differ by more than 0.15 goals, show both and say which tool each came from.
- Safe versus ceiling: safe = highest P60 (4 or 5 of 5 recent matches at 60 minutes) with a captain xP within 1 point of the top; ceiling = highest E[g] + E[a]. Say that one match is a tiny sample whichever you pick.
- Vice-captain: the player with the next-highest P60 who plays at a different time or in a different match from the captain, from the schedule dates. If two shortlist players kick off together, say the vice is a weaker cover.
- Differential: only if the user pasted ownership; a player owned by under about 15 percent with a captain xP within 1 point of the top is the differential option; say the points gap is the price of the differential.
- Never call a pick 'guaranteed' and never claim a player is fit without a log showing minutes.

## Output template
```
Gameweek <n> captain ranking (<season>)
1. <player> (<team> v <opp>, <date>): captain xP <x> | P60 <a>/5 | E[g] model <g>, market <m> | E[a] <a> | opp goals conceded <c>/gm
2. ...
Safe pick: <name> (why) | Ceiling pick: <name> | Vice: <name> (kicks off <date>)
Differential: <name: ownership <pasted>> / needs ownership
Availability: <each player's status and recent_squads from get_availability> | Unchecked: <injury news not yet visible in squads>
Data: get_schedule, get_player_games, get_player, get_team_stats, get_player_props | season <yyyy>
```

## When there is no game or no line
If a shortlist player has no fixture this gameweek (blank), his captain xP is zero and he is out. If no price exists for a match, use the model goal expectation only and say so. Under 5 league matches this season, mark the rates 'not enough data'. In an international break, say there are no fixtures and rank for the next gameweek when the schedule shows it.

## Rules
- Fantasy rankings and advice only: no betting language. A prop line is used here only as the market's median projection of a player's stat.
- The user's scoring and roster settings come from the user; if unstated, say which default you used and show the formula so they can swap it.

- Every number names its tool (the Data line, plus inline where it could be doubted) and every rate shows its sample size and season convention.
- Do not add names, roles, injuries or storylines from memory; if a tool did not return it, leave it out.
- If a tool returns too little, say "not enough data" and stop; do not estimate or borrow another sample.
- Sequences (last 5, recent form, a run of results) are quoted in the order the tool returns them, with dates; never retell them from memory. Lists in the answer must match the tool rows.
- Out of credits: if a call is refused for credits, pass on the API's message as it comes (it says when free credits refill and how to add more), and stop; don't retry or switch tools to get around it.
