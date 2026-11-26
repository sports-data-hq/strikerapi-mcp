---
name: soccer-player-props-research
description: "Use when the user asks about soccer player prop prices as information: 'Haaland anytime scorer odds', 'how often does Saka score when he's priced like this', 'Palmer shots on target line', 'who's most likely to score in Arsenal Chelsea according to the market', 'did the price move on Salah', 'is Isak worth looking at for first goalscorer'. Shows prices by sportsbook, the market's no-margin probability, the player's record against similar lines with minutes, and how the price moved. Covers anytime, first and last goalscorer, shots, shots on target, assists and cards where the league holds them. Never says which side to take. Not for match previews (see soccer-match-preview), expected goals stories (see soccer-xg-breakdown) or general odds math (see odds-market-analysis)."
metadata:
  version: 2.0.0
  author: SportsDataHQ
---

# Soccer player props research

Prop prices are the market's view of one player's match. This skill lays them out, turns them into probability, and checks them against what the player actually did, with minutes and starting status as the main context. It never recommends a side, a stake or a bet.

## Before you start
- For anything across matches, seasons or players, apply the sports-data-preflight checks first (ids from search, the season convention, one league, enough data).
- Tools come from the StrikerAPI MCP server. Prices move until kick-off; always call, never recall.
- Leagues with props: the Premier League (`soccer`), La Liga, Serie A, the Bundesliga, Ligue 1, MLS and the Champions League. Each tool's league list is the authority.
- Seasons are named by the year they start (2025 = 2025-26); MLS, NWSL, Liga MX, Brazil and Argentina use the calendar year.

## Default path
1. **Resolve the player and match.** `search_players(name)` (1 cr); `get_schedule(league)` (2 cr) for the match id.
2. **Prices now.** `get_player_props(game_id, market)` (10 cr before kick-off, 20 after), or `player_id` with `from_date` for one player across matches. Markets: `player_goal_scorer_anytime`, `player_first_goal_scorer`, `player_last_goal_scorer`, `player_shots`, `player_shots_on_target`, `player_assists`, `player_to_receive_card`. Yes/no markets carry the Yes price in `over_odds`.
3. **What the market implies.** `get_prop_probabilities(game_id, market, player_id)` (10 cr): average implied and no-margin probability. A market whose books post only the Yes price has no no-margin figure; say so and show the average implied one.
4. **Track record.** `get_prop_history(player_id, market)` (10 cr per 25 matches covered): closing price and result per match; a null result means he wasn't in the squad or on the pitch (a void).
5. **Will he start?** `get_lineups(game_id)` (5 cr) once lineups are out; before that, `get_player_games(player_id, season)` (5 cr) for starts and minutes. A sub appearance is a different bet on the same name.
6. **Movement (optional).** `get_prop_movement` (10 cr).
7. **The base rate (optional).** `get_prop_backtest(market, ...)` (25 cr) with filters such as `team_role`, `home_away`, `fair_min`/`fair_max`, `team` or `player_id`, for how often a kind of prop hit ("anytime scorer for home favourites"). Report its sample size, interval and per-season split, and never present a past rate as a pick.

## How to report a record
- Scoring props: goals per 90 and non-penalty goals per 90 from `get_player_games`, with minutes; say whether he takes penalties if the data shows it.
- Hit rate = hits / matches with a price; show n and the window ("scored in 7 of 19 priced starts, 37%").
- Separate starts from sub appearances. Below 10 matches, say the sample is too small.
- A past hit rate is not a forecast, and a price is not a prediction of ours.

## Output template
```
<Player> (<club>) | <market> | <match, date>
Prices now: <book: price> | best and worst
Market implies: <x%> (no-margin)
Record: <hits>/<n> priced matches (<%>), <window> | starts only: <a>/<b> | goals per 90 <g> (np <npg>), <minutes> min
Starting: <confirmed / probable / unknown>
Data: search_players, get_player_props, get_prop_probabilities, get_prop_history, get_lineups | season <yyyy>
```

## Rules
- Information only: no picks, leans, "value", stakes, payouts or sportsbook links.
- Every number names its tool; every rate shows n.
- No price posted yet: say so; don't borrow another market or match.
