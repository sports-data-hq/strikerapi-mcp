---
name: soccer-season-simulation
description: "Use when the user wants simulated odds for how a league season finishes: 'who wins the league', 'chance of finishing top four', 'relegation odds', 'simulate the rest of the season', 'how likely is Arsenal to win the title', 'what are Spurs' chances of Europe'. Runs a Monte Carlo of the remaining fixtures from our club Elo ratings, the current table and a stated draw and home-advantage assumption, and reports finishing-position probabilities with the method. Not for one match (see soccer-match-preview), shot quality (see soccer-xg-breakdown) or a club's style (see soccer-team-style-profile)."
metadata:
  version: 2.0.0
  author: SportsDataHQ
---

# Soccer season simulation

A league table is a sum of results already played plus fixtures to come. This skill simulates the fixtures to come from our Elo ratings and reports how often each club finishes in each place. The method is simple and is stated every time; it is our model, not the market's.

## Before you start
- For anything across games, seasons or players, apply the sports-data-preflight checks first (ids from search, the season convention, one league, enough data).
- Tools come from the StrikerAPI MCP server (`https://mcp.strikerapi.com/mcp`). Call them; never fill in a number from memory.
- `season` is named by the year it starts (2025 = 2025-26); MLS, NWSL, Liga MX, Brazil and Argentina use calendar years. Pass `league` (soccer is the Premier League; others by key). Clubs accept names or abbreviations (`list_teams`, 1 cr).
- Inputs: the current table (`get_standings`), our ratings (`get_ratings`: a 100-point gap is about a 64% expected score for the stronger side at a neutral venue) and the remaining fixtures (`list_games`, or `get_schedule` for the next 1 to 30 days). If the remaining fixtures cannot all be listed, say so and simulate only what was listed; do not invent fixtures.
- Home advantage and the draw rate are not returned by a tool. Use stated assumptions: take the league's draw share from the standings so far (draws divided by matches played) and say that no home-advantage adjustment is applied unless the user gives one. Label both as assumptions.
- Run the simulation with code execution or a short script if you have it. If you have no way to run code, say you cannot run a Monte Carlo and give only the table and the points needed; do not write made-up probabilities.

## Default path (about 6 to 12 credits)
1. **Table.** `get_standings(season, league)` (2 cr): points (with deductions), played, goal difference, qualification and relegation notes.
2. **Ratings.** `get_ratings(league)` (2 cr): our Elo for every club, as of the latest match.
3. **Remaining fixtures.** `list_games(season, league)` (2 cr) for scheduled matches, or `get_schedule(days: 30, league)` (2 cr) for the next month. Count them against 38 minus played (or the league's length) per club and report any gap.
4. **Simulate.** For each fixture compute the home club's expected score from the rating gap, E = 1 / (1 + 10^(-gap / 400)), set the draw probability to the league draw share, split the remainder so the home club's expected score equals E, draw a result, add points, repeat 10,000 times or more, and rank by points; goal difference is not simulated, so break ties by a coin toss.
5. **Report.** Probability of each finishing place or band (title, top four, European places, relegation) with the simulation count.

## Method statement (always include)
State: ratings are our Elo as of the latest match and do not update inside the simulation; results are drawn from the expected-score formula above with the stated draw share; no home advantage unless given; goal difference is not simulated, so tiebreaks are a coin toss; injuries, rotation and fixture congestion are not modelled. Report the number of simulations and, if it is under about 10,000, that small probabilities are rough.

## Output template
```
<League> | season <yyyy> | matches played <n> of <N> | simulations <k>
Method: Elo expected score, draw share <d>% from the table, no home advantage (assumption), ties by coin toss
Title: <club %>, <club %>, ...
Top four / European places: <club %> ...
Relegation: <club %> ...
Points needed (optional): <club: points to reach the last safe place in the median simulation>
Caveats: <fixtures missing, injuries not modelled>
Data: get_standings, get_ratings, list_games or get_schedule | <league>, season <yyyy>
```

## Rules
- Say "our simulation" for these probabilities and "the market" for prices; never blend them.
- Odds are not needed; show them only if the user asks, from `get_odds` for a match, as the market's number.
- Probabilities under 1% are shown as "under 1%", not as exact values.
- If remaining fixtures are missing, say how many clubs and matches are affected.
- Every number names its tool (the Data line, plus inline where it could be doubted) and every rate shows its sample size.
- If a tool returns too little, say "not enough data" and stop; do not estimate or borrow another sample.
- Odds and prices are information about the market only: no picks, leans, stakes, payouts or sportsbook links.

## Example prompts
- "Who wins the Premier League, simulated?"
- "What are Tottenham's chances of finishing top four?"
- "Relegation odds in La Liga with 10 games left"
